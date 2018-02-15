# Code Smells

## Warnings

Compiler warnings provide some basic checking for code smells.

Unfortunately, our recommendation here is a bit verbose.  We have is to put the
following in each of your `lib.rs`, `main.rs`, and test `.rs` files:

```rust,ignore
#![deny(const_err,
        dead_code,
        illegal_floating_point_literal_pattern,
        improper_ctypes,
        non_camel_case_types,
        non_shorthand_field_patterns,
        non_snake_case,
        non_upper_case_globals,
        no_mangle_generic_items,
        overflowing_literals,
        path_statements,
        patterns_in_fns_without_body,
        plugin_as_library,
        private_in_public,
        private_no_mangle_fns,
        private_no_mangle_statics,
        renamed_and_removed_lints,
        stable_features,
        unconditional_recursion,
        unions_with_drop_fields,
        unknown_lints,
        unreachable_code,
        unreachable_patterns,
        unused_allocation,
        unused_assignments,
        unused_attributes,
        unused_comparisons,
        unused_features,
        unused_imports,
        unused_macros,
        unused_must_use,
        unused_mut,
        unused_parens,
        unused_unsafe,
        unused_variables,
        while_true)]
```

You can update this list for new versions of rustc by:
1. Run `rustc -W help`
2. Grab all `default=warn` warnings
3. Paste them here, deleting `warnings`, and any with `deprecated` in the name
   since new rustc versions can introduce new deprecations.

Some people like to add a `dev` feature to their project as a quick way to silence these warnings.

`Cargo.toml`:

```toml
[features]
dev = []
```

`lib.rs` (etc):

```rust,ignore
#![cfg_attr(not(feature="dev"), deny(const_err,
        //...
        while_true))]
```

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

[clippy]: https://github.com/rust-lang-nursery/rust-clippy

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
