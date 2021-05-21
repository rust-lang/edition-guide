# Panic macro consistency

## Summary

## Details


The `panic!()` macro is one of Rust's most well known macros.
However, it has [some subtle surprises](https://github.com/rust-lang/rfcs/blob/master/text/3007-panic-plan.md)
that we can't just change due to backwards compatibility.

```rust,ignore
panic!("{}", 1); // Ok, panics with the message "1"
panic!("{}"); // Ok, panics with the message "{}"
```

The `panic!()` macro only uses string formatting when it's invoked with more than one argument.
When invoked with a single argument, it doesn't even look at that argument.

```rust,ignore
let a = "{";
println!(a); // Error: First argument must be a format string literal
panic!(a); // Ok: The panic macro doesn't care
```

(It even accepts non-strings such as `panic!(123)`, which is uncommon and rarely useful.)

This will especially be a problem once
[implicit format arguments](https://rust-lang.github.io/rfcs/2795-format-args-implicit-identifiers.html)
are stabilized.
That feature will make `println!("hello {name}")` a short-hand for `println!("hello {}", name)`.
However, `panic!("hello {name}")` would not work as expected,
since `panic!()` doesn't process a single argument as format string.

To avoid that confusing situation, Rust 2021 features a more consistent `panic!()` macro.
The new `panic!()` macro will no longer accept arbitrary expressions as the only argument.
It will, just like `println!()`, always process the first argument as format string.
Since `panic!()` will no longer accept arbitrary payloads,
[`panic_any()`](https://doc.rust-lang.org/stable/std/panic/fn.panic_any.html)
will be the only way to panic with something other than a formatted string.

In addition, `core::panic!()` and `std::panic!()` will be identical in Rust 2021.
Currently, there are some historical differences between those two,
which can be noticable when switching `#![no_std]` on or off.