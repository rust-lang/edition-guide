# MSVC toolchain support

![Minimum Rust version: 1.2](https://img.shields.io/badge/Minimum%20Rust%20Version-1.2-brightgreen.svg)

At the release of Rust 1.0, we only supported the GNU toolchain on Windows. With the
release of Rust 1.2, we introduced initial support for the MSVC toolchain. After that,
as support matured, we eventually made it the default choice for Windows users.

The difference between the two matters for interacting with C. If you're using a library
built with one toolchain or another, you need to match that with the appropriate Rust
toolchain. If you're not sure, go with MSVC; it's the default for good reason.

To use this feature, simply use Rust on Windows, and the installer will default to it.
If you'd prefer to switch to the GNU toolchain, you can install it with Rustup:

```console
$ rustup toolchain install stable-x86_64-pc-windows-gnu
```