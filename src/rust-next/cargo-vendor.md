# cargo vendor

Initially added: ![Minimum Rust version: 1.37](https://img.shields.io/badge/Minimum%20Rust%20Version-1.37-brightgreen.svg)

After being available [as a separate crate][vendor-crate] for years, the `cargo vendor` command is now integrated directly into Cargo. The command fetches all your project's dependencies unpacking them into the `vendor/` directory, and shows the configuration snippet required to use the vendored code during builds.

There are multiple cases where `cargo vendor` is already used in production: the Rust compiler `rustc` uses it to ship all its dependencies in release tarballs, and projects with monorepos use it to commit the dependencies' code in source control.

[vendor-crate]: https://crates.io/crates/cargo-vendor
