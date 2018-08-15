# `cargo install` for easy installation of tools

![Minimum Rust version: 1.5](https://img.shields.io/badge/Minimum%20Rust%20Version-1.5-brightgreen.svg)

Cargo has grown a new `install` command. This is intended to be used for installing
new subcommands for Cargo, or tools for Rust developers. This doesn't replace the need
to build real, native packages for end-users on the platforms you support.

For example, this guide is created with [`mdbook`](https://crates.io/crates/mdbook). You
can install it on your system with

```console
$ cargo install mdbook
```

And then use it with

```console
$ mdbook --help
```

As an example of extending Cargo, you can use the [`cargo-update`](https://crates.io/crates/cargo-update)
package. To install it:

```console
$ cargo install cargo-update
```

This will allow you to use this command, which checks everything you've `cargo install`'d and
updates it to the latest version:

```console
$ cargo install-update -a
```