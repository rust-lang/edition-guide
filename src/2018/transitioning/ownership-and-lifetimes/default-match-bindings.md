# Default match bindings

Have you ever had a borrowed `Option<T>` and tried to match on it? You
probably wrote this:

```rust,ignore
let s: &Option<&str> = Some("hello");

match s {
    Some(s) => println!("s is: {}", s),
    _ => (),
};
```

In older Rusts, this would fail to compile, you would have had to write this:

```rust,ignore
let s: &Option<&str> = Some("hello");

match s {
    &Some(ref s) => println!("s is: {}", s),
    _ => (),
};
```

As of [1.26](nicer-match-bindings), Rust will infer the `&`s and `ref`s, and your original code will Just Work.

[nicer-match-bindings]: https://blog.rust-lang.org/2018/05/10/Rust-1.26.html#nicer-match-bindings

## More details

The mental model of `match` has shifted a bit with this change; `match s` now have
a "default binding mode". This mode is one of `move`, `ref`, or `ref mut`. Before
this was implemented, `match` was always in `move` mode, that is, by default it would
assume that anything was moved when referred to, and you added `&` and `ref` to
change that.

Now, it can default to one of the three modes. So above, since `s` is a reference,
the compiler assumes that the names should be bound by reference, rather than by moving.
Similarly, if it was a mutable reference, it would be assumed that things were bound
by mutable reference.
