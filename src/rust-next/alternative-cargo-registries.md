# Alternative Cargo registries

Initially added: ![Minimum Rust version: 1.34](https://img.shields.io/badge/Minimum%20Rust%20Version-1.34-brightgreen.svg)

For various reasons, you may not want to publish code to crates.io, but you
may want to share it with others. For example, maybe your company writes Rust
code that's not open source, but you'd still like to use these internal
packages.

Cargo supports alternative registries by settings in `.cargo/config`:

```toml
[registries]
my-registry = { index = "https://my-intranet:7878/git/index" }
```

When you want to depend on a package from another registry, you add that
in to your `Cargo.toml`:

```toml
[dependencies]
other-crate = { version = "1.0", registry = "my-registry" }
```

To learn more, check out the [registries section of the Cargo
book](https://doc.rust-lang.org/nightly/cargo/reference/registries.html).
