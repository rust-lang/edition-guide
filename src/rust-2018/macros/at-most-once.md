# At most one repetition

![Minimum Rust version: 1.32](https://img.shields.io/badge/Minimum%20Rust%20Version-1.32-brightgreen.svg) for 2018 edition

![Minimum Rust version: 1.37](https://img.shields.io/badge/Minimum%20Rust%20Version-1.37-brightgreen.svg) for 2015 edition

In Rust 2018, we have made a couple of changes to the macros-by-example syntax.

1. We have added a new Kleene operator `?` which means "at most one"
   repetition. This operator does not accept a separator token.
2. We have disallowed using `?` as a separator to remove ambiguity with `?`.

For example, consider the following Rust 2015 code:

```rust2018
macro_rules! foo {
    ($a:ident, $b:expr) => {
        println!("{}", $a);
        println!("{}", $b);
    }
    ($a:ident) => {
        println!("{}", $a);
    }
}
```

Macro `foo` can be called with 1 or 2 arguments; the second one is optional,
but you need a whole other matcher to represent this possibility. This is
annoying if your matchers are long. In Rust 2018, one can simply write the
following:

```rust2018
macro_rules! foo {
    ($a:ident $(, $b:expr)?) => {
        println!("{}", $a);

        $(
            println!("{}", $b);
         )?
    }
}
```
