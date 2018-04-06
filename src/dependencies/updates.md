# Keeping Up on Dependencies

Challenges with dependencies:
- Knowing when new versions are available.
- Evaluating how new versions impact your users.
- Validating your crate, including maintaining your oldest supported Rust version.

Thankfully this is all automatable and has been, thanks to [Dependabot][dependabot].

[dependabot]: https://dependabot.com/

## Dependabot

Recommended setup:
1. Verify your CI configuration
2. Sign up
3. Add your repos
4. Lower update frequency to once a week (to balance updates with CI load)

Your process will look like:
1. Get a PR for a `Cargo.toml` or `Cargo.lock` update
2. Review the release notes, changelog, and/or commit history for impact
3. Wait until your CI gives the green light
4. Merge

If an update introduces a conflict, Dependabot will automatically recreate the update.

### Verify your CI Configuration

- [Oldest-supported rustc][testing] is used to catch dependencies that require newer rustc's
- Don't run your CI on Dependabot branches to avoid double-running them

[testing]: /pr/testing.html

#### Limiting Branches

A snippet for `.travis.yml`:
```yml
branches:
  only:
  # Release tags
  - /^v\d+\.\d+\.\d+.*$/
  - master
```

A snippet for `appveyor.yml`:
```yml
branches:
  only:
  # Release tags
  - /^v\d+\.\d+\.\d+.*$/
  - master
```
