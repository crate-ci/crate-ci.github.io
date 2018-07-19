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
  - env: RUSTFLAGS="-D warnings"
    rust: 1.24.0  # `stable`: Locking down for consistent behavior
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
contributor's PR, frustrating and possibly alienating them.

## Lints

Linters are extra tools for checking for code smells.  They aren't regular warnings either because
- Slower to analyze
- False positives

[clippy][clippy] is the standard linter for the Rust ecosystem.

See also [example-clippy][example-clippy].

[clippy]: https://github.com/rust-lang-nursery/rust-clippy
[example-clippy]: https://github.com/crate-ci/example-clippy

### TravisCI

Like with [rustfmt](pr/rustfmt.html), you only need `clippy` running in one job
and we recommend running it on Travis rather than Appveyor because Travis
supports your jobs running in parallel.

We'll be adding the following to your `.travis.yml`:

```yml
matrix:
  include:
  - env: CLIPPY
    rust: nightly-2018-07-17
    install:
      - rustup component add clippy-preview
    script:
      - cargo clippy --all-features -- -D clippy
```

Highlights
- `matrix: include:` is allowing us to define a complete one-off build job.
  - This will run in parallel to your tests, giving you quicker feedback.
  - No other job output will be in here, making it easier to see the results.
- Clippy changes don't break the CI due to pinning the nightly version

[travis-cache]: https://docs.travis-ci.com/user/caching
