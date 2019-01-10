# Aborting on panic

![Minimum Rust version: 1.10](https://img.shields.io/badge/Minimum%20Rust%20Version-1.10-brightgreen.svg)

By default, Rust programs will unwind the stack when a `panic!` happens. If you'd prefer an
immediate abort instead, you can configure this in `Cargo.toml`:

```toml
[profile.dev]
panic = "abort"

[profile.release]
panic = "abort"
```

Why might you choose to do this? By removing support for unwinding, you'll
get smaller binaries. You will lose the ability to catch panics. Which choice
is right for you depends on exactly what you're doing.
