# Default match bindings

![Minimum Rust version: 1.26](https://img.shields.io/badge/Minimum%20Rust%20Version-1.26-brightgreen.svg)

Have you ever had a borrowed `Option<T>` and tried to match on it? You
probably wrote this:

```rust,ignore
let s: &Option<String> = &Some("hello".to_string());

match s {
    Some(s) => println!("s is: {}", s),
    _ => (),
};
```

In Rust 2015, this would fail to compile, and you would have to write the
following instead:

```rust,ignore
// Rust 2015

let s: &Option<String> = &Some("hello".to_string());

match s {
    &Some(ref s) => println!("s is: {}", s),
    _ => (),
};
```

Rust 2018, by contrast, will infer the `&`s and `ref`s, and your original
code will Just Work.

This affects not just `match`, but patterns everywhere, such as in `let`
statements, closure arguments, and `for` loops.

## More details

The mental model of patterns has shifted a bit with this change, to bring it
into line with other aspects of the language. For example, when writing a
`for` loop, you can iterate over borrowed contents of a collection by
borrowing the collection itself:

```rust,ignore
let my_vec: Vec<i32> = vec![0, 1, 2];

for x in &my_vec { ... }
```

The idea is that an `&T` can be understood as a *borrowed view of `T`*, and
so when you iterate, match, or otherwise destructure a `&T` you get a
borrowed view of its internals as well.

More formally, patterns have a "binding mode," which is either by value
(`x`), by reference (`ref x`), or by mutable reference (`ref mut x`). In Rust
2015, `match` always started in by-value mode, and required you to explicitly
write `ref` or `ref mut` in patterns to switch to a borrowing mode. In Rust
2018, the type of the value being matched informs the binding mode, so that
if you match against an `&Option<String>` with a `Some` variant, you are put
into `ref` mode automatically, giving you a borrowed view of the internal
data. Similarly, `&mut Option<String>` would give you a `ref mut` view.
