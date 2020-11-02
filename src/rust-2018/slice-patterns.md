# Slice patterns

![Minimum Rust version: 1.26](https://img.shields.io/badge/Minimum%20Rust%20Version-1.26-brightgreen.svg)

Have you ever tried to pattern match on the contents and structure of a slice?
Rust 2018 will let you do just that.

For example, say we want to accept a list of names and respond to that with a
greeting. With slice patterns, we can do that easy as pie with:

```rust
fn main() {
    greet(&[]);
    // output: Bummer, there's no one here :(
    greet(&["Alan"]);
    // output: Hey, there Alan! You seem to be alone.
    greet(&["Joan", "Hugh"]);
    // output: Hello, Joan and Hugh. Nice to see you are exactly 2!
    greet(&["John", "Peter", "Stewart"]);
    // output: Hey everyone, we seem to be 3 here today.
}

fn greet(people: &[&str]) {
    match people {
        [] => println!("Bummer, there's no one here :("),
        [only_one] => println!("Hey, there {}! You seem to be alone.", only_one),
        [first, second] => println!(
            "Hello, {} and {}. Nice to see you are exactly 2!",
            first, second
        ),
        _ => println!("Hey everyone, we seem to be {} here today.", people.len()),
    }
}
```

Now, you don't have to check the length first.

We can also match on arrays like so:

```rust
let arr = [1, 2, 3];

assert_eq!("ends with 3", match arr {
    [_, _, 3] => "ends with 3",
    [a, b, c] => "ends with something else",
});
```

## More details

### Exhaustive patterns

In the first example, note in particular the `_ => ...` pattern.
Since we are matching on a slice, it could be of any length, so we need a
*"catch all pattern"* to handle it. If we forgot the `_ => ...` or
`identifier => ...` pattern, we would instead get an error saying:

```ignore
error[E0004]: non-exhaustive patterns: `&[_, _, _]` not covered
```

If we added a case for a slice of size `3` we would instead get:

```ignore
error[E0004]: non-exhaustive patterns: `&[_, _, _, _]` not covered
```

and so on...

### Arrays and exact lengths

In the second example above, since arrays in Rust are of known lengths,
we have to match on exactly three elements.
If we try to match on 2 or 4 elements,we get the errors:

```ignore
error[E0527]: pattern requires 2 elements but array has 3
```

and

```ignore
error[E0527]: pattern requires 4 elements but array has 3
```

### In the pipeline

[the tracking issue]: https://github.com/rust-lang/rust/issues/23121

When it comes to slice patterns, more advanced forms are planned but
have not been stabilized yet. To learn more, follow [the tracking issue].
