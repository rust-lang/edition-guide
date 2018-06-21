# What are Editions?

Rust ships releases on a six-week cycle. This means that users get a constant
stream of new features. This is much faster than updates for other languages,
but this also means that each update is smaller.  After a while, all of those
tiny changes add up. But, from release to release, it can be hard to look back
and say *"Wow, between Rust 1.10 and Rust 1.20, Rust has changed a lot!"*

Every two or three years, we'll be producing a new *edition* of Rust. Each
edition brings together the features that have landed into a clear package, with
fully updated documentation and tooling. New editions ship through the usual
release process.

This serves different purposes for different people:

- For active Rust users, it brings together incremental changes into an
  easy-to-understand package.

- For non-users, it signals that some major advancements have landed, which
  might make Rust worth another look.

- For those developing Rust itself, it provides a rallying point for the project as a
  whole.

## Compatibility

When a new edition becomes available in the compiler, crates must explicitly opt
in to it to take full advantage. This opt in enables editions to contain
incompatible changes, like adding a new keyword that might conflict with
identifiers in code, or turning warnings into errors. The Rust compiler can
link crates of any editions together. Therefore, if you're using Rust 2015, and
one of your dependencies uses Rust 2018, it all works just fine. The opposite
situation works as well.

## Trying out the 2018 edition

At the time of writing, there are two editions: 2015 and 2018. 2015 is today's
Rust; Rust 2018 will ship later this year. To give Rust 2018 a try, you can
add this feature to your crate root:

```rust
// Opt in to unstable features expected for Rust 2018
#![feature(rust_2018_preview)]

// Opt in to warnings about new 2018 idioms
#![feature(rust_2018_idioms)]
```

and opt in to the new edition in your `Cargo.toml`:

```toml
cargo-features = ["edition"]

[package]
edition = '2018'
```

Like all `#![feature(..)]` flags, this will only work on nightly Rust.
When nightly compilers are released, more functionality is enabled,
so you may experience new warnings, features, and other things.
This is something to keep in mind and is exactly why nightly is unstable!
