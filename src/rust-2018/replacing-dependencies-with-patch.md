# Replacing dependencies with patch

![Minimum Rust version: 1.21](https://img.shields.io/badge/Minimum%20Rust%20Version-1.21-brightgreen.svg)

The `[patch]` section of your `Cargo.toml` can be used when you want to
override certain parts of your dependency graph.

> Cargo has a `[replace]` feature that is similar; while we don't intend to deprecate
> or remove `[replace]`, you should prefer `[patch]` in all circumstances.

So what’s it look like? Let’s say we have a Cargo.toml that looks like this:

```toml
[dependencies]
foo = "1.2.3"
```

In addition, our `foo` package depends on a `bar` crate, and we find a bug in `bar`.
To test this out, we’d download the source code for `bar`, and then update our
`Cargo.toml`:

```toml
[dependencies]
foo = "1.2.3"

[patch.crates-io]
bar = { path = '/path/to/bar' }
```

Now, when you `cargo build`, it will use the local version of `bar`, rather
than the one from crates.io that `foo` depends on. You can then try out your
changes, and fix that bug!

For more details, see [the documentation for
`patch`](https://doc.rust-lang.org/cargo/reference/manifest.html#the-patch-section).