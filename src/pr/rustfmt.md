# Style Enforcement

Pre-requisites:
- [Testing](pr/testing.html) is setup

[rustfmt][rustfmt] is the standard tool for automating code formatting.  You
can run it manually to clean up your code, integrate it into your text editor
to do it automatically, or have it verify if your code is formatted correctly.
That last role is what we want to leverage to reduce reduce the burden of
handling PRs.

See also [example-rustfmt][example-rustfmt].

[example-rustfmt]: https://github.com/crate-ci/example-rustfmt
[rustfmt]: https://github.com/rust-lang-nursery/rustfmt

## Specifying Your Style

We recommend you use the default style as that will be most universal within the Rust ecosystem.

With that said, it would be beneficial to capture a snapshot of that style.
- If users are on different versions of `rustfmt` with different defaults, it will help minimize conflicts.
- If a new `rustfmt` is released with a new default, this could cause PRs to start failing, frustrating contributors.

You can lock down your style by running:
```bash
rustfmt --dump-default-config .rustfmt.toml
```

## TravisCI

Unlike your tests, there is little value in running more than one job to check
the style. We recommend running it on Travis rather than Appveyor because
Travis supports your jobs running in parallel.

We'll be adding the following to your `.travis.yml`:
```yml
matrix:
  include:
  - rust: 1.24.0  # `stable`: Locking down for consistent behavior
    env: RUSTFMT
    install:
    - rustup component add rustfmt-preview
    script:
    - cargo fmt -- --write-mode=diff
```

Highlights:
- `matrix: include:` is allowing us to define a complete one-off build job.
  - This will run in parallel to your tests, giving you quicker feedback.
  - No other job output will be in here, making it easier to see the results.
- `rust: 1.24.0`: We run a specific version of Rust to get its version of `rustfmt`
  - Locking down to a specific version is helpful to avoid behavior changes, even if bug fixes, from breaking PRs.
- `env: RUSTFMT`: This is purely here because Travis will put it in the job summary, making it easier to distinguish this job from others
