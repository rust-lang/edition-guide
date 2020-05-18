# The dbg! macro

![Minimum Rust version: 1.32](https://img.shields.io/badge/Minimum%20Rust%20Version-1.32-brightgreen.svg)

The `dbg!` macro provides a nicer experience for debugging than `println!`:

```rust
fn main() {
    let x = 5;

    dbg!(x);
}
```

If you run this program, you'll see:

```text
[src/main.rs:4] x = 5
```

You get the file and line number of where this was invoked, as well as the
name and value. Additionally, `println!` prints to the standard output, so you
really should be using `eprintln!` to print to standard error. `dbg!` does the
right thing and goes to stderr.

It even works in more complex circumstances. Consider this factorial example:

```rust
fn factorial(n: u32) -> u32 {
    if n <= 1 {
        n
    } else {
        n * factorial(n - 1)
    }
}
```

If we wanted to debug this, we might write it like this with `eprintln!`:

```rust
fn factorial(n: u32) -> u32 {
    eprintln!("n: {}", n);

    if n <= 1 {
        eprintln!("n <= 1");

        n
    } else {
        let n = n * factorial(n - 1);

        eprintln!("n: {}", n);

        n
    }
}
```

We want to log `n` on each iteration, as well as have some kind of context
for each of the branches. We see this output for `factorial(4)`:

```text
n: 4
n: 3
n: 2
n: 1
n <= 1
n: 2
n: 6
n: 24
```

This is servicable, but not particularly great. Maybe we could work on how we
print out the context to make it more clear, but now we're not debugging our
code, we're figuring out how to make our debugging code better.

Consider this version using `dbg!`:

```rust
fn factorial(n: u32) -> u32 {
    if dbg!(n <= 1) {
        dbg!(1)
    } else {
        dbg!(n * factorial(n - 1))
    }
}
```

We simply wrap each of the various expressions we want to print with the macro. We get this output instead:

```text
[src/main.rs:3] n <= 1 = false
[src/main.rs:3] n <= 1 = false
[src/main.rs:3] n <= 1 = false
[src/main.rs:3] n <= 1 = true
[src/main.rs:4] 1 = 1
[src/main.rs:5] n * factorial(n - 1) = 2
[src/main.rs:5] n * factorial(n - 1) = 6
[src/main.rs:5] n * factorial(n - 1) = 24
[src/main.rs:11] factorial(4) = 24
```

Because the `dbg!` macro returns the value of what it's debugging, instead of
`eprintln!` which returns `()`, we need to make no changes to the structure of
our code. Additionally, we have vastly more useful output.
