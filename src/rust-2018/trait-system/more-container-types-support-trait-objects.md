# More container types support trait objects

![Minimum Rust version: 1.2](https://img.shields.io/badge/Minimum%20Rust%20Version-1.2-brightgreen.svg)

In Rust 1.0, only certain, special types could be used to create [trait
objects](https://doc.rust-lang.org/book/ch17-02-trait-objects.html).

With Rust 1.2, that restriction was lifted, and more types became able to do this. For example,
`Rc<T>`, one of Rust's reference-counted types:

```rust
use std::rc::Rc;

trait Foo {}

impl Foo for i32 {

}

fn main() {
    let obj: Rc<dyn Foo> = Rc::new(5);
}
```

This code would not work with Rust 1.0, but now works.

> If you haven't seen the `dyn` syntax before, see the section on it. For
> versions that do not support it, replace `Rc<dyn Foo>` with `Rc<Foo>`.
