# Changes to the prelude

## Summary

- The [`Future`] and [`IntoFuture`] traits are now part of the prelude.
- This might make calls to trait methods ambiguous which could make some code fail to compile.

[`Future`]: ../../std/future/trait.Future.html
[`IntoFuture`]: ../../std/future/trait.IntoFuture.html

## Details

The [prelude of the standard library](../../std/prelude/index.html) is the module containing everything that is automatically imported in every module.
It contains commonly used items such as `Option`, `Vec`, `drop`, and `Clone`.

The Rust compiler prioritizes any manually imported items over those from the prelude,
to make sure additions to the prelude will not break any existing code.
For example, if you have a crate or module called `example` containing a `pub struct Option;`,
then `use example::*;` will make `Option` unambiguously refer to the one from `example`;
not the one from the standard library.

However, adding a _trait_ to the prelude can break existing code in a subtle way.
For example, a call to `x.poll()` which comes from a `MyPoller` trait might fail to compile if `std`'s `Future` is also imported, because the call to `poll` is now ambiguous and could come from either trait.

As a solution, Rust 2024 will use a new prelude.
It's identical to the current one, except for the following changes:

- Added:
    - [`std::future::Future`][`Future`]
    - [`std::future::IntoFuture`][`IntoFuture`]

## Migration

### Conflicting trait methods

When two traits that are in scope have the same method name, it is ambiguous which trait method should be used. For example:

```rust,edition2021
trait MyPoller {
    // This name is the same as the `poll` method on the `Future` trait from `std`.
    fn poll(&self) {
        println!("polling");
    }
}

impl<T> MyPoller for T {}

fn main() {
    // Pin<&mut async {}> implements both `std::future::Future` and `MyPoller`.
    // If both traits are in scope (as would be the case in Rust 2024),
    // then it becomes ambiguous which `poll` method to call
    core::pin::pin!(async {}).poll();
}
```

We can fix this so that it works on all editions by using fully qualified syntax:

```rust,ignore
fn main() {
    // Now it is clear which trait method we're referring to
    <_ as MyPoller>::poll(&core::pin::pin!(async {}));
}
```

The [`rust_2024_prelude_collisions`] lint will automatically modify any ambiguous method calls to use fully qualified syntax. This lint is part of the `rust-2024-compatibility` lint group, which will automatically be applied when running `cargo fix --edition`. To migrate your code to be Rust 2024 Edition compatible, run:

```sh
cargo fix --edition
```

Alternatively, you can manually enable the lint to find places where these qualifications need to be added:

```rust
// Add this to the root of your crate to do a manual migration.
#![warn(rust_2024_prelude_collisions)]
```

[`rust_2024_prelude_collisions`]: ../../rustc/lints/listing/allowed-by-default.html#rust-2024-prelude-collisions
