# In-band lifetimes

When writing an `fn` declaration, if a lifetime appears that is not already in
scope, it is taken to be a new binding, i.e. treated as a parameter to the
function.

So, in Rust 2015, you'd write:

```rust,ignore
fn two_args<'bar>(foo: &Foo, bar: &'bar Bar) -> &'bar Baz
```

In Rust 2018, you'd write:

```rust,ignore
fn two_args(foo: &Foo, bar: &'bar Bar) -> &'bar Baz
```

In other words, you can drop the explicit lifetime parameter declaration, and
instead simply start using a new lifetime name to connect lifetimes together.
