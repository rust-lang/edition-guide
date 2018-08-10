# Improved error messages

![Minimum Rust version: 1.12](https://img.shields.io/badge/Minimum%20Rust%20Version-1.12-brightgreen.svg)

We're always working on error improvements, and there are little improvements
in almost every Rust version, but in Rust 1.12, a significant overhaul of the
error message system was created.

For example, here's some code that produces an error:

```rust,ignore
fn main() {
    let mut x = 5;

    let y = &x;

    x += 1;
}
```

Here's the error in Rust 1.11:

```text
foo.rs:6:5: 6:11 error: cannot assign to `x` because it is borrowed [E0506]
foo.rs:6     x += 1;
             ^~~~~~
foo.rs:4:14: 4:15 note: borrow of `x` occurs here
foo.rs:4     let y = &x;
                      ^
foo.rs:6:5: 6:11 help: run `rustc --explain E0506` to see a detailed explanation
```

Here's the error in Rust 1.28:

```text
error[E0506]: cannot assign to `x` because it is borrowed
 --> foo.rs:6:5
  |
4 |     let y = &x;
  |              - borrow of `x` occurs here
5 |
6 |     x += 1;
  |     ^^^^^^ assignment to borrowed `x` occurs here

error: aborting due to previous error
```

This error isn't terribly different, but shows off how the format has changed. It shows
off your code in context, rather than just showing the text of the lines themselves.