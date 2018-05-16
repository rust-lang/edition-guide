# Macro changes

Now that `extern crate` is gone, there's a problem: to import macros, you
need to put `#[macro_use]` on the `extern crate` declaration.

To fix this issue, you can use `use` to import macros from extern crates.

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
#[macro_use]
extern crate bar;

fn main() {
    baz!();
}
```

Now, you write:

```rust,ignore
#![feature(rust_2018_preview, use_extern_macros)]

use bar::baz;

fn main() {
    baz!();
}
```

This moves `macro_rules` macros to be a bit closer to other kinds of items.

## More details

This only works for `extern crate`; if you write a macro in the same crate,
nothing changes.