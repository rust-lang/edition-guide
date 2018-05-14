# `'_`, the anonymous lifetime

New in edition 2018, the "anonymous lifetime" feature, allows you to explicitly
mark where a lifetime is elided. To do this, you can use the special lifetime
`'_` much like you can explicitly mark that a type is inferred with the syntax
`let x: _ = ..;`.

Let's say, for whatever reason, that we have a simple wrapper around `&'a str`:

```rust
struct StrWrap<'a>(&'a str);
```

In edition 2015, you might have written:

```rust
use std::fmt;

fn make_wrapper(string: &str) -> StrWrap {
    StrWrap(string)
}

impl<'a> fmt::Debug for StrWrap<'a> {
    fn fmt(&self, fmt: &mut fmt::Formatter) -> fmt::Result {
        fmt.write_str(self.0)
    }
}
```

In edition 2018, you can instead write:

```rust
#![feature(rust_2018_preview)]

use std::fmt;

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

In the second snippet above, we've used `-> StrWrap`.
However, unless you take a look at the definition of `StrWrap`,
it is not clear that the returned value is actually borrowing something.
Therefore, starting with edition 2018, it is deprecated, for non-reference-types
(types other than `&` and `&mut`), to leave of the lifetime parameters.
Instead, where you previously wrote `-> StrWrap`,
you should now, in edition 2018, write `-> StrWrap<'_>`.

What exactly does `'_` mean? It depends on the context!
In output contexts, as in the return type of `make_wrapper`,
it refers to a single lifetime for  all "output" locations.
In input contexts, a fresh lifetime is generated for each "input location".
More concretely, to understand input contexts, consider the following example:

```rust
struct Foo<'a, 'b: 'a> {
    field: &'a &'b str,
}

impl<'a, 'b: 'a> Foo<'a, 'b> {
    // some methods...
}
```

We can rewrite this as:

```rust
impl Foo<'_, '_> {
    // some methods...
}
```

This is the same, because for each `'_`, a fresh lifetime is generated.
Finally, the relationship `'a: 'b` which the struct requires must be upheld.

For more details, see the [tracking issue on In-band lifetime bindings](https://github.com/rust-lang/rust/issues/44524).
