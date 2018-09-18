# Cargo workspaces for multi-package projects

![Minimum Rust version: 1.12](https://img.shields.io/badge/Minimum%20Rust%20Version-1.12-brightgreen.svg)

Cargo used to have two levels of organization:

* A *package* contains one or more crates
* A crate has one or more modules

Cargo now has an additional level:

* A *workspace* contains one or more packages

This can be useful for larger projects. For example, [the `futures` package]
is a *workspace* that contains many related packages:

* futures
* futures-util
* futures-io
* futures-channel

and more.

[the `futures` package]: https://github.com/rust-lang-nursery/futures-rs

Workspaces allow these packages to be developed individually, but they share
a single set of dependencies, and therefore have a single target directory
and a single `Cargo.lock`.

For more details about workspaces, please see [the Cargo documentation](https://doc.rust-lang.org/stable/cargo/reference/manifest.html#the-workspace-section).
