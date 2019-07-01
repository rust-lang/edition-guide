# The Rust Edition Guide

[![Build Status](https://api.travis-ci.com/rust-lang-nursery/edition-guide.svg?branch=master)](https://travis-ci.com/rust-lang-nursery/edition-guide)

This book explains the concept of "editions", major new eras in [Rust]'s
development. You can [read the book
online](https://doc.rust-lang.org/nightly/edition-guide/).

[Rust]: https://www.rust-lang.org/

## License

The Edition Guide is dual licensed under `MIT`/`Apache2`, just like Rust itself.
See the `LICENSE-*` files in this repository for more details.

## Building locally

You can also build the book and read it locally if you'd like.

### Requirements

Building the book requires [mdBook] 0.2. To get it:

[mdBook]: https://github.com/azerupi/mdBook

```bash
$ cargo install mdbook
```

### Building

The most straight-forward way to build and view the book locally is to use the following command:
```bash
$ mdbook serve
```

This serves the book at http://localhost:3000, and rebuilds it on changes.
You can now view the book in your web browser. If you make changes to the book's source code,
you should only need to refresh your browser to see them.

_Firefox:_

```shell
$ firefox http://localhost:3000                       # Linux
$ open -a "Firefox" http://localhost:3000             # OS X
$ Start-Process "firefox.exe" http://localhost:3000   # Windows (PowerShell)
$ start firefox.exe http://localhost:3000             # Windows (Cmd)
```

_Chrome:_

```shell
$ google-chrome http://localhost:3000                 # Linux
$ open -a "Google Chrome" http://localhost:3000       # OS X
$ Start-Process "chrome.exe" http://localhost:3000    # Windows (PowerShell)
$ start chrome.exe http://localhost:3000              # Windows (Cmd)
```

To run the tests:

```bash
$ mdbook test
```
