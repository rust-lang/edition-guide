# Cargo can use a local registry replacement

![Minimum Rust version: 1.12](https://img.shields.io/badge/Minimum%20Rust%20Version-1.12-brightgreen.svg)

Cargo finds its packages in a "source". The default source is [crates.io](https://crates.io). However, you
can choose a different source in your `.cargo/config`:

```toml
[source.crates-io]
replace-with = 'my-awesome-registry'

[source.my-awesome-registry]
registry = 'https://github.com/my-awesome/registry-index'
```

This configuration means that instead of using crates.io, Cargo will query
the `my-awesome-registry` source instead (configured to a different index
here). This alternate source *must be the exact same* as the crates.io index.
Cargo assumes that replacement sources are exact 1:1 mirrors in this respect,
and the following support is designed around that assumption.

When generating a lock file for crate using a replacement registry, the
original registry will be encoded into the lock file. For example in the
configuration above, all lock files will still mention crates.io as the
registry that packages originated from. This semantically represents how
crates.io is the source of truth for all crates, and this is upheld because
all replacements have a 1:1 correspondance.

Overall, this means that no matter what replacement source you're working
with, you can ship your lock file to anyone else and you'll all still have
verifiably reproducible builds!

This has enabled tools like
[`cargo-vendor`](https://github.com/alexcrichton/cargo-vendor) and
[`cargo-local-registry`](https://github.com/alexcrichton/cargo-local-registry),
which are often useful for "offline builds." They prepare the list of all
Rust dependencies ahead of time, which lets you ship them to a build machine
with ease.