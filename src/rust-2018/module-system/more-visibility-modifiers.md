# More visibility modifiers

![Minimum Rust version: 1.18](https://img.shields.io/badge/Minimum%20Rust%20Version-1.18-brightgreen.svg)

You can use the `pub` keyword to make something a part of a module's public interface. But in
addition, there are some new forms:

```rust,ignore
pub(crate) struct Foo;

pub(in a::b::c) struct Bar;
```

The first form makes the `Foo` struct public to your entire crate, but not
externally. The second form is similar, but makes `Bar` public for one other
module, `a::b::c` in this case.