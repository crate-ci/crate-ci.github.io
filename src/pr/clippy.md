# Code Smells

Pre-requisites:
- [Testing](pr/testing.html) is setup

## Warnings

Compiler warnings provide some basic checking for code smells.

There are many ways to check for warnings.  We recommend the followig:

In each of your `lib.rs`, `main.rs`, and test `.rs` files:

```rust,ignore
#![warn(warnings)]
```

In `.travis.yml`:

```yml
matrix:
  include:
  - rust: 1.24.0  # `stable`: Locking down for consistent behavior
    env: RUSTFLAGS="-D warnings"
    script:
    - cargo check --tests
```

Highlights
- Doesn't slow people down during prototyping by turning warnings into errors.
- Contributors see all warnings they will be accountable for due to `warn(warnings)`.
- Warning changes don't break the CI due to `rust: 1.24.0`

A major downside of this:
- Only run on one target

See also [example-warn][example-warn].

[example-warn]: https://github.com/crate-ci/example-warn

### Why Avoid The Simple Answer

The seemingly easy answer for checking for warnings is to either:

```bash
RUSTFLAGS="-D warnings" cargo build
```

or in each of your `lib.rs`, `main.rs`, and test `.rs` files:

```rust
#![deny(warnings)]
```

The reason you don't want to do this in your CI process is that new versions of
Rust can add and remove warnings, causing the build to break on your
contributor's PR, frustrating and possibly alienating the,.  

## Lints

Linters are extra tools for checking for code smells.  They aren't regular warnings either because
- Slower to analyze
- False positives

[clippy][clippy] is the standard linter for the Rust ecosystem.

See also [example-clippy][example-clippy].

[clippy]: https://github.com/rust-lang-nursery/rust-clippy
[example-clippy]: https://github.com/crate-ci/example-clippy

### TravisCI

Like with [rustfmt](pr/rustfmt.html), you only need `clippy` running in on job
and we recommend running it on Travis rather than Appveyor because Travis
supports your jobs running in parallel.

We'll be adding the following to your `.travis.yml`:

```yml
matrix:
  include:
  - rust: nightly-2018-01-12
    env: CLIPPY_VERSION=0.0.179
    install:
    - travis_wait cargo install clippy --version $CLIPPY_VERSION || echo "clippy already installed"
    script:
    - cargo clippy -- -D clippy
```

Highlights
- `matrix: include:` is allowing us to define a complete one-off build job.
  - This will run in parallel to your tests, giving you quicker feedback.
  - No other job output will be in here, making it easier to see the results.
- `rust: nightly-2018-01-12` and `CLIPPY_VERSION`:
  - These need to be kept in lock step with each other.
  - Travis caches where `clippy` gets installed to.
    - This makes the builds faster not having to recompile `clippy` every time.
    - On a fresh container, we need `travis_wait` to avoid timeouts in Travis
    - On a cached container, `cargo install` will quickly error out because `clippy` is already install
    - When changing `CLIPPY_VERSION` (or any `matrix` `env`), Travis will [invalidate the cache][travis-cache].

[travis-cache]: https://docs.travis-ci.com/user/caching
