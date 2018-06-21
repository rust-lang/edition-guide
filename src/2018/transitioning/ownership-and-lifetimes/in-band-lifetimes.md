# In-band lifetimes

When writing an `fn` declaration, if a lifetime appears that is not already in
scope, it is taken to be a new binding, i.e. treated as a parameter to the
function.

So, in Rust 2015, you'd write:

```rust,ignore
fn two_args<'b>(arg1: &Foo, arg2: &'b Bar) -> &'b Baz
```

In Rust 2018, you'd write:

```rust,ignore
fn two_args(arg1: &Foo, arg2: &'b Bar) -> &'b Baz
```

In other words, you can drop the explicit lifetime parameter declaration, and
instead simply start using a new lifetime name to connect lifetimes together.
