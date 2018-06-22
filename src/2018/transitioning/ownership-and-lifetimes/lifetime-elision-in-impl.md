# Lifetime elision in `impl`

When writing an `impl`, you can mention lifetimes without them being bound in
the argument list. This is similar to
[in-band-lifetimes](/2018/transitioning/ownership-and-lifetimes/in-band-lifetimes.md)
but for `impl`s.

In Rust 2015:

```rust,ignore
impl<'a> Iterator for MyIter<'a> { ... }
impl<'a, 'b> SomeTrait<'a> for SomeType<'a, 'b> { ... }
```

In Rust 2018:

```rust,ignore
impl Iterator for MyIter<'iter> { ... }
impl SomeTrait<'tcx, 'gcx> for SomeType<'tcx, 'gcx> { ... }
```

## More details

To show off how this combines with in-band lifetimes in methods/functions, in Rust 2015:

```rust,ignore
// Rust 2015

impl<'a> MyStruct<'a> {
    fn foo(&self) -> &'a str

    // we have to use 'b here because it conflicts with the 'a above. If this weren't part
    // of an `impl`, we'd be using `'a`.
    fn bar<'b>(&self, arg: &'b str) -> &'b str
}
```

in Rust 2018:

```rust,ignore
// Rust 2018

// no need for the repetition of 'a
impl MyStruct<'a> {

    // this works just like before
    fn foo(&self) -> &'a str

    // we can refer to 'arg, rather than conflicting with 'a
    fn bar(&self, arg: &str) -> &'arg str
}
```
