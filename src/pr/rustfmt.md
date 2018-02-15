# Style Enforcement

Pre-requisites:
- [Testing](pr/testing.html) is setup

[rustfmt][rustfmt] is the standard tool for automating code formatting.  You
can run it manually to clean up your code, integrate it into your text editor
to do it automatically, or have it verify if your code is formatted correctly.
That last role is what we want to leverage to reduce reduce the burden of
handling PRs.

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
  - rust: stable
    env: RUSTFMT_VERSION=0.8.6
    install:
      - travis_wait cargo install rustfmt --version $RUSTFMT_VERSION || echo "rustfmt already installed"
    script:
      - cargo fmt -- --write-mode=diff
```

Highlights:
- `matrix: include:` is allowing us to define a complete one-off build job.
  - This will run in parallel to your tests, giving you quicker feedback.
  - No other job output will be in here, making it easier to see the results.
- `RUSTFMT_VERSION`: We install a specific instance of `rustfmt`.
  - Locking down to a specific version is helpful to avoid behavior changes, even if bug fixes, from breaking PRs.
  - This is showing running a version on `stable` channel.  There are newer versions on `nightly`.
  - Soon this will be integrated into `rustup` like `cargo` and friends, making this easier.
- `travis_wait cargo install ...`: Travis caches where `rustfmt` gets installed to.
  - This makes the builds faster not having to recompile `rustfmt` every time.
  - On a fresh container, we need `travis_wait` to avoid timeouts in Travis
  - On a cached container, `cargo install` will quickly error out because `rustfmt` is already install
  - When changing `RUSTFMT_VERSION` (or any `matrix` `env`), Travis will [invalidate the cache][travis-cache].

[travis-cache]: https://docs.travis-ci.com/user/caching
