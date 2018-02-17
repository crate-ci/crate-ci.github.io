# CHANGELOG

Providing a meaningful changelog is helpful for your users, particularly for
recording breaking changes and how people can migrate between versions.

## Writing a Changelog with `clog`

[`clog`][clog] can help you write your changelog.

Pre-requisites:
- Commits in [conventional style][commit-style].
  - [gitcop][gitcop] is a bot to enforce a specific commit style.
- Releases are tagged.,
- The previous release is the most recent tag.

Steps
1. Install clog: `cargo install clig-cli`
2. Run `clog --setversion <X>.<Y>.<Z>`
3. Massage the output as needed.

[clog]: https://github.com/clog-tool/clog-cli
[gitcop]: https://gitcop.com/
[commit-style]: https://github.com/conventional-changelog/conventional-changelog/blob/a5505865ff3dd710cf757f50530e73ef0ca641da/conventions/angular.md
