# Non-lexical lifetimes

![Minimum Rust version: 1.31](https://img.shields.io/badge/Minimum%20Rust%20Version-1.31-brightgreen.svg)

The borrow checker has been enhanced to accept more code, via a mechanism
called "non-lexical lifetimes." Consider this example:

```rust,ignore
fn main() {
    let mut x = 5;

    let y = &x;

    let z = &mut x;
}
```

In older Rust, this is a compile-time error:

```text
error[E0502]: cannot borrow `x` as mutable because it is also borrowed as immutable
 --> src/main.rs:5:18
  |
4 |     let y = &x;
  |              - immutable borrow occurs here
5 |     let z = &mut x;
  |                  ^ mutable borrow occurs here
6 | }
  | - immutable borrow ends here
```

This is because lifetimes follow "lexical scope"; that is, the borrow from `y` is
considered to be held until `y` goes out of scope at the end of `main`, even though
we never use `y` again. This code is fine, but the borrow checker could not handle it.

Today, this code will compile just fine.

## Better errors

What if we did use `y`, like this?

```rust,ignore
fn main() {
    let mut x = 5;
    let y = &x;
    let z = &mut x;
    
    println!("y: {}", y);
}
```

Here's the error:

```text
error[E0502]: cannot borrow `x` as mutable because it is also borrowed as immutable
 --> src/main.rs:5:18
  |
4 |     let y = &x;
  |              - immutable borrow occurs here
5 |     let z = &mut x;
  |                  ^ mutable borrow occurs here
...
8 | }
  | - immutable borrow ends here
```

With non-lexical lifetimes, the error changes slightly:

```text
error[E0502]: cannot borrow `x` as mutable because it is also borrowed as immutable
 --> src/main.rs:5:13
  |
4 |     let y = &x;
  |             -- immutable borrow occurs here
5 |     let z = &mut x;
  |             ^^^^^^ mutable borrow occurs here
6 |     
7 |     println!("y: {}", y);
  |                       - borrow later used here
```

Instead of pointing to where `y` goes out of scope, it shows you where
the conflicting borrow occurs. This makes these sorts of errors *far* easier to debug.
