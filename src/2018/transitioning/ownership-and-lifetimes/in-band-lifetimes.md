# In-band lifetimes

When writing a fn declaration, if a lifetime appears that is not already in
scope, it is taken to be a new binding, i.e. treated as a parameter to the
function.

So, in Rust 2015, you'd write:

```rust,ignore
fn two_args<'b>(arg1: &Foo, arg2: &'b Bar) -> &'b Baz
```

In Rust 2018, you'd write:

```rust,ignore
fn two_args(arg1: &Foo, arg2: &Bar) -> &'arg2 Baz
```

Here, `'arg2` was not defined. Rust sees that you have a parameter, `arg2`, that
should have a lifetime, and so the lifetime of that reference.

To put it in words, the former says:

> `two_args` is a function with one lifetime, `'b`. Its first parameter is a
> reference to a `Foo`, , and its second parameter is a reference to a `Bar`,
> bound by the lifetime `'b`, as its second argument. It returns a reference to
> a `Baz` bound by the lifetime `'b`.

And the latter says:

> `two_args` is a function. Its first parameter is a reference to a `Foo`,
> and its second parameter is a reference to a `Bar`. It returns a reference to
> a `Baz` with the same lifetime as the second parameter.

This says what it means, as opposed to implying it. Instead of defining a
lifetime, and then connecting things together, you say "this is the lifetime
of that."

## More details

More complicated signatures work too:

```rust,ignore
// before
fn two_lifetimes<'a, 'b: 'a>(arg1: &'a Foo, arg2: &'b Bar) -> &'a Quux<'b>

// after
fn two_lifetimes(arg1: &Foo, arg2: &Bar) -> &'arg1 Quux<'arg2>
```

Here, the `'b: 'a` is inferred, as it's the only possible way that `'arg1`
and `'arg2` could relate.