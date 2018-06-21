# `'_`, the anonymous lifetime

Rust 2018 allows you to explicitly mark where a lifetime is elided, for types
where this elision might otherwise be unclar. To do this, you can use the
special lifetime `'_` much like you can explicitly mark that a type is inferred
with the syntax `let x: _ = ..;`.

[1.26]: https://github.com/rust-lang/rust/blob/master/RELEASES.md#version-1260-2018-05-10

Let's say, for whatever reason, that we have a simple wrapper around `&'a str`:

```rust
struct StrWrap<'a>(&'a str);
```

In Rust 2015, you might have written:

```rust
// Rust 2015

use std::fmt;

# struct StrWrap<'a>(&'a str);

fn make_wrapper(string: &str) -> StrWrap {
    StrWrap(string)
}

impl<'a> fmt::Debug for StrWrap<'a> {
    fn fmt(&self, fmt: &mut fmt::Formatter) -> fmt::Result {
        fmt.write_str(self.0)
    }
}
```

In Rust 2018, you can instead write:

```rust
// Rust 2018

#![feature(rust_2018_preview)]

# use std::fmt;
# struct StrWrap<'a>(&'a str);

fn make_wrapper(string: &str) -> StrWrap<'_> {
    StrWrap(string)
}

impl fmt::Debug for StrWrap<'_> {
    fn fmt(&self, fmt: &mut fmt::Formatter) -> fmt::Result {
        fmt.write_str(self.0)
    }
}
```

## More details

In the Rust 2015 snippet above, we've used `-> StrWrap`. However, unless you take
a look at the definition of `StrWrap`, it is not clear that the returned value
is actually borrowing something. Therefore, starting with Rust 2018, it is
deprecated to leave off the lifetime parameters for non-reference-types (types
other than `&` and `&mut`). Instead, where you previously wrote `-> StrWrap`,
you should now write `-> StrWrap<'_>`, making clear that borrowing is occurring.

What exactly does `'_` mean? It depends on the context!
In output contexts, as in the return type of `make_wrapper`,
it refers to a single lifetime for  all "output" locations.
In input contexts, a fresh lifetime is generated for each "input location".
More concretely, to understand input contexts, consider the following example:

```rust
// Rust 2015

struct Foo<'a, 'b: 'a> {
    field: &'a &'b str,
}

impl<'a, 'b: 'a> Foo<'a, 'b> {
    // some methods...
}
```

We can rewrite this as:

```rust
// Rust 2018

#![feature(rust_2018_preview)]

# struct Foo<'a, 'b: 'a> {
#     field: &'a &'b str,
# }

impl Foo<'_, '_> {
    // some methods...
}
```

This is the same, because for each `'_`, a fresh lifetime is generated.
Finally, the relationship `'a: 'b` which the struct requires must be upheld.

For more details, see the [tracking issue on In-band lifetime bindings](https://github.com/rust-lang/rust/issues/44524).
