# Testing

Pre-requisites:
- Crate exists
- `cargo test` runs relevant tests

## Questions to Consider

### Platforms to Support?

Target platforms are a combination of:
- OS
- CPU
- 32-bit or 64-bit
- (Linux) libc version
- (Windows) MSys or MSVC

These platforms fall into several major categories
- Desktop
- "Embedded": Phones, Raspberry Pis, etc
- Embedded: Those running `no_std`

If you are creating a library, the ideal situation is to support as many of these as possible.

If you are creating a program, it is mostly a matter of having at least one
form of binary on the systems you care about.  It would be nice to support more
in case a contributor's development environment is different than yours.

### Rust versions to Support?

Some clients might not be able to always use the latest release.  Maintainers have taken different approaches to this
- Don't support older versions
- Support `N` releases back
- Only require compiler upgrades on major or minor

In addition to numbered, releases, Rust has the following release labels
- `stable`: The current release
- `beta`: The next release
- `nightly`: Bleeding edge with feature flags to enable "unstable" features
  - "unstable" from the perspective of API/behavior compatibility; this does not speak to whether it is runtime stable

Recommendation:
- Support 2 releases back
- Features requiring `nightly` are hidden behind a feature flag

### Platform / Rust Coverage?

You can run your tests on every version of Rust and across every platform at
the cost of slower feedback on your PRs and increasing load on your chosen CI
service.

Recommendation:
- Test all platforms on "2 releases back" and `stable`
- Test the minimum platforms on `beta` and `nightly`

## Running Tests on PRs

There are several CI hosts that will help you run tests on your CI

### TravisCI

Supports:
- Linux
- Mac

Steps:
1. [Sign up for TravisCI](https://travis-ci.org/)
2. View your repositories by clicking on your name
3. Follow the steps for "Review and add" your organization, if needed
4. "Sync account" if your repo isn't showing up
5. Toggle the switch for your repo
6. Add a `.travis.yml` to your repo

This is a good starting `.travis.yml`:
```yml
sudo: false
language: rust
rust:
- 1.21.0  # Two releases back
- stable
- beta
- nightly

install:
- rustc -Vv
- cargo -V

script:
- cargo check --verbose
- cargo test --verbose

cache:
  cargo: true
```

Highlights
- `sudo: false`: Allows Travis to do some optimizations
- `install`: print tool versions for traceability
- `cargo check`: Faster turn around time on results but `cargo test` can't
  reuse the results.  Useful for when doing test builds of different feature
  combinations
- `cache:`: [Cache the cargo registry and build][travis-cache]

[travis-cache]: https://docs.travis-ci.com/user/caching/#Rust-Cargo-cache

### Appveyor

Supports:
- Windows

This is a good starting `appveyor.yml`:
```yml
environment:
  global:
    CHANNEL: stable
    #APPVEYOR_CACHE_SKIP_RESTORE: true  # Uncomment when caching causes problems

  matrix:
    # Two releases back
    - TARGET: i686-pc-windows-msvc
      CHANNEL: 1.21.0
    - TARGET: i686-pc-windows-gnu
      CHANNEL: 1.21.0
    - TARGET: x86_64-pc-windows-msvc
      CHANNEL: 1.21.0
    - TARGET: x86_64-pc-windows-gnu
      CHANNEL: 1.21.0
    # Stable channel
    - TARGET: i686-pc-windows-msvc
      CHANNEL: stable
    - TARGET: i686-pc-windows-gnu
      CHANNEL: stable
    - TARGET: x86_64-pc-windows-msvc
      CHANNEL: stable
    - TARGET: x86_64-pc-windows-gnu
      CHANNEL: stable
    # Beta channel
    - TARGET: x86_64-pc-windows-msvc
      CHANNEL: beta
    - TARGET: x86_64-pc-windows-gnu
      CHANNEL: beta
    # Nightly channel
    - TARGET: x86_64-pc-windows-msvc
      CHANNEL: nightly
    - TARGET: x86_64-pc-windows-gnu
      CHANNEL: nightly

install:
  - ps: >-
      $Env:PATH += ';C:\msys64\usr\bin'
  - curl -sSf -o rustup-init.exe https://win.rustup.rs/
  - rustup-init.exe -y --default-host %TARGET% --default-toolchain %CHANNEL%
  - set PATH=%PATH%;C:\Users\appveyor\.cargo\bin
  - rustc -Vv
  - cargo -V

test_script:
  - cargo check --verbose
  - cargo test --verbose

cache:
  - C:\Users\appveyor\.cargo\registry
  - target

notifications:
  - provider: Email
    on_build_success: false

# Building is done in the test phase, so we disable Appveyor's build phase.
build: false
```

Highlights:
- `install`
  - Install rust using Curl (distributed with msys64)
  - Print tool versions for traceability
