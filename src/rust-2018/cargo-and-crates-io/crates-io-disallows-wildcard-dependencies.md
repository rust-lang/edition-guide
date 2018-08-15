# Crates.io disallows wildcard dependencies

![Minimum Rust version: 1.6](https://img.shields.io/badge/Minimum%20Rust%20Version-1.6-brightgreen.svg)

Crates.io will not allow you to upload a package with a wildcard dependency.
In other words, these:

```toml
[dependencies]
regex = "*"
```

A wildcard dependency means that you work with any possible version of your
dependency. This is highly unlikely to be true, and would cause unnecessary
breakage in the ecosystem.

Instead, depend on a version range. For example, `^` is the default, so
you could use

```toml
[dependencies]
regex = "1.0.0"
```

instead. `>`, `<=`, and all of the other, non-`*` ranges work as well.