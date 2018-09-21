# Macro changes

![Minimum Rust version: beta](https://img.shields.io/badge/Minimum%20Rust%20Version-beta-orange.svg)

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

use bar::baz;

fn main() {
    baz!();
}
```

This moves `macro_rules` macros to be a bit closer to other kinds of items.

Note that you'll still need `#[macro_use]` to use macros you've defined
in your own crate; this feature only works for importing macros from
external crates.

## Procedural macros

When using procedural macros to derive traits, you will have to name the macro
that provides the custom derive. This generally matches the name of the trait,
but check with the documentation of the crate providing the derives to be sure.

For example, with Serde you would have written

```rust,ignore
// Rust 2015
extern crate serde;
#[macro_use] extern crate serde_derive;

#[derive(Serialize, Deserialize)]
struct Bar;
```

Now, you write instead:

```rust,ignore
// Rust 2018
use serde_derive::{Serialize, Deserialize};

#[derive(Serialize, Deserialize)]
struct Bar;
```


## More details

This only works for macros defined in external crates.
For macros defined locally, `#[macro_use] mod foo;` is still required, as it was in Rust 2015.
