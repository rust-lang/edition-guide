# MUSL support for fully static binaries

![Minimum Rust version: 1.1](https://img.shields.io/badge/Minimum%20Rust%20Version-1.1-brightgreen.svg)

By default, Rust will statically link all Rust code. However, if you use the
standard library, it will dynamically link to the system's `libc`
implementation.

If you'd like a 100% static binary, the [`MUSL
libc`](https://www.musl-libc.org/) can be used on Linux.

## Installing MUSL support

To add support for MUSL, you need to choose the correct target. [The forge
has a full list of
targets](https://forge.rust-lang.org/release/platform-support.html) supported,
with a number of ones using `musl`.

If you're not sure what you want, it's probably `x86_64-unknown-linux-musl`,
for 64-bit Linux. We'll be using this target in this guide, but the
instructions remain the same for other targets, just change the name wherever
we mention the target.

To get support for this target, you use `rustup`:

```console
$ rustup target add x86_64-unknown-linux-musl
```

This will install support for the default toolchain; to install for other toolchains,
add the `--toolchain` flag. For example:

```console
$ rustup target add x86_64-unknown-linux-musl --toolchain=nightly
```

## Building with MUSL

To use this new target, pass the `--target` flag to Cargo:

```console
$ cargo build --target x86_64-unknown-linux-musl
```

The binary produced will now be built with MUSL!
