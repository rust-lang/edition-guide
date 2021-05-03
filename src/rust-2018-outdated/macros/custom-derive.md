# Custom Derive

![Minimum Rust version: 1.15](https://img.shields.io/badge/Minimum%20Rust%20Version-1.15-brightgreen.svg)

In Rust, you’ve always been able to automatically implement some traits
through the derive attribute:

```rust
#[derive(Debug)]
struct Pet {
    name: String,
}
```

The `Debug` trait is then implemented for `Pet`, with vastly less boilerplate. For example, without `derive`, you'd have
to write this:

```rust
use std::fmt;

struct Pet {
    name: String,
}

impl fmt::Debug for Pet {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            Pet { name } => {
                let mut debug_trait_builder = f.debug_struct("Pet");

                let _ = debug_trait_builder.field("name", name);

                debug_trait_builder.finish()
            }
        }
    }
}
```

Whew!

However, this only worked for traits provided as part of the standard
library; it was not customizable. But now, you can tell Rust what to do when
someone wants to derive your trait. This is used heavily in popular crates
like [serde](https://serde.rs/) and [Diesel](http://diesel.rs/).

For more, including learning how to build your own custom derive, see [The
Rust Programming
Language](https://doc.rust-lang.org/book/ch19-06-macros.html#how-to-write-a-custom-derive-macro).