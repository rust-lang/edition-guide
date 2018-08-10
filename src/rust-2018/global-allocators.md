# Global allocators

![Minimum Rust version: 1.28](https://img.shields.io/badge/Minimum%20Rust%20Version-1.28-brightgreen.svg)

Allocators are the way that programs in Rust obtain memory from the system at
runtime. Previously, Rust did not allow changing the way memory is obtained,
which prevented some use cases. On some platforms, this meant using jemalloc,
on others, the system allocator, but there was no way for users to control
this key component. With 1.28.0, the `#[global_allocator]` attribute is now
stable, which allows Rust programs to set their allocator to the system
allocator, as well as define new allocators by implementing the `GlobalAlloc`
trait.

The default allocator for Rust programs on some platforms is jemalloc. The
standard library now provides a handle to the system allocator, which can be
used to switch to the system allocator when desired, by declaring a static
and marking it with the `#[global_allocator]` attribute.

```rust
use std::alloc::System;

#[global_allocator]
static GLOBAL: System = System;

fn main() {
    let mut v = Vec::new();
    // This will allocate memory using the system allocator.
    v.push(1);
}
```

However, sometimes you want to define a custom allocator for a given
application domain. This is also relatively easy to do by implementing the
`GlobalAlloc` trait. You can read more about how to do this in [the
documentation](https://doc.rust-lang.org/std/alloc/trait.GlobalAlloc.html).