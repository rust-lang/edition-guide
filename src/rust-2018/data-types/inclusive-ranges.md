# `..=` for inclusive ranges

![Minimum Rust version: 1.26](https://img.shields.io/badge/Minimum%20Rust%20Version-1.26-brightgreen.svg)

Since well before Rust 1.0, you’ve been able to create exclusive ranges with
.. like this:

```
for i in 1..3 {
    println!("i: {}", i);
}
```

This will print `i: 1` and then `i: 2`. Today, you can now create an
inclusive range, like this:

```rust
for i in 1..=3 {
    println!("i: {}", i);
}
```

This will print `i: 1` and then `i: 2` like before, but also `i: 3`; the
three is included in the range. Inclusive ranges are especially useful if you
want to iterate over every possible value in a range. For example, this is a
surprising Rust program:

```rust,compile_fail
fn takes_u8(x: u8) {
    // ...
}

fn main() {
    for i in 0..256 {
        println!("i: {}", i);
        takes_u8(i);
    }
}
```

What does this program do? The answer: it fails to compile. The error we get
when compiling has a hint:

```text
error: literal out of range for u8
 --> src/main.rs:6:17
  |
6 |     for i in 0..256 {
  |                 ^^^
  |
  = note: #[deny(overflowing_literals)] on by default
```

That’s right, since `i` is a `u8`, this overflows, and the compiler produces
an error.

We can do this with inclusive ranges, however:

```rust
fn takes_u8(x: u8) {
    // ...
}

fn main() {
    for i in 0..=255 {
        println!("i: {}", i);
        takes_u8(i);
    }
}
```

This will produce those 256 lines of output you might have been expecting.
