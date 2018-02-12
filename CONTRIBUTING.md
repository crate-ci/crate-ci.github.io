# crate-ci contribution guidelines

Thank you for your interest in making crate-ci documentation
better! We'd love to have your contribution. We expect all contributors to
abide by the [Rust code of conduct], which you can find at that link. If you
have questions or concerns, feel free to reach out to us.

[Rust code of conduct]: https://www.rust-lang.org/en-US/conduct.html

## License

crate-ci docs are dual licenced under the MIT and Apache 2.0 licenses, and so are all
contributions. Please see the [`LICENSE-MIT`] and [`LICENSE-APACHE`] files in
this directory for more details.

[`LICENSE-MIT`]: https://github.com/rust-lang/rust-by-example/blob/master/LICENSE-MIT
[`LICENSE-APACHE`]: https://github.com/rust-lang/rust-by-example/blob/master/LICENSE-APACHE

## Pull Requests

To make changes to crate-ci docs, please send in pull requests on GitHub to the `master`
branch. We'll review them and either merge or request changes. Travis CI tests
everything as well, so you may get feedback from it too.

If you make additions or other changes to a pull request, feel free to either amend
previous commits or only add new ones, however you prefer. We may ask you to squash
your commits before merging, depending.


## Issue Tracker

You can find the issue tracker [on
GitHub](https://github.com/crate-ci/crate-ci.github.io/issues). If you've found a
problem with the crate-ci docs, please open an issue there.

We use the following labels:

* `enhancement`: This is for any request for new sections or functionality.
* `bug`: This is for anything that's in the docs, but incorrect or not working.
* `discussion`: A discussion about improving something in RBE; this may lead to new
  enhancement or bug issues.

## Development workflow

To build the docs, [install Rust], and then:

```bash
$ cargo install mdbook
$ git clone https://github.com/crate-ci/crate-ci.github.io
$ cd crate-ci.github.io
$ mdbook build
```

[install Rust]: http://rust-lang.org/install.html

The files will be in the `book` directory at the top-level; `mdbook serve` will
open the contents in your web browser.

To run the tests:

```bash
$ mdbook test
```

If you're adding a new chapter, you'll need to edit `src\SUMMARY.md` to add it. If
you're tweaking an existing example, you'll need to edit the corresponding file; check
`src\SUMMARY.md` to see a mapping of where chapters go to files.
