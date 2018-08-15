# `cargo rustc` for passing arbitrary flags to rustc

![Minimum Rust version: 1.1](https://img.shields.io/badge/Minimum%20Rust%20Version-1.1-brightgreen.svg)

`cargo rustc` is a new subcommand for Cargo that allows you to pass arbitrary
`rustc` flags through Cargo.

For example, Cargo does not have a way to pass unstable flags built-in. But
if we'd like to use `print-type-sizes` to see what layout information our
types have. We can run this:

```console
$ cargo rustc -- -Z print-type-sizes
```

And we'll get a bunch of output describing the size of our types.

## Note

`cargo rustc` only passes these flags to invocations of your crate, and not to any `rustc`
invocations used to build dependencies. If you'd like to do that, see `$RUSTFLAGS`.