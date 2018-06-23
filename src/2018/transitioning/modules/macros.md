# Macro changes

In Rust 2018, you can import specific macros from external crates via `use`
statements, rather than the old `#[macro_use]` attribute.

For example, consider a `bar` crate that implements a `baz!` macro. In
`src/lib.rs`:

```rust
#[macro_export]
macro_rules! baz {
    () => ()
}
```

In your crate, you would have written

```rust,ignore
// Rust 2015

#[macro_use]
extern crate bar;

fn main() {
    baz!();
}
```

Now, you write:

```rust,ignore
// Rust 2018
#![feature(rust_2018_preview, use_extern_macros)]

use bar::baz;

fn main() {
    baz!();
}
```

This moves `macro_rules` macros to be a bit closer to other kinds of items.

## More details

This only works for macros defined in external crates.
For macros defined locally, `#[macro_use] mod foo;` is still required, as it was in Rust 2015.
