# The `?` operator for easier error handling

![Minimum Rust version: 1.13](https://img.shields.io/badge/Minimum%20Rust%20Version-1.13-brightgreen.svg) for `Result<T, E>`

![Minimum Rust version: 1.22](https://img.shields.io/badge/Minimum%20Rust%20Version-1.22-brightgreen.svg) for `Option<T>`

Rust has gained a new operator, `?`, that makes error handling more pleasant
by reducing the visual noise involved. It does this by solving one simple
problem. To illustrate, imagine we had some code to read some data from a
file:

```rust
# use std::{io::{self, prelude::*}, fs::File};
fn read_username_from_file() -> Result<String, io::Error> {
    let f = File::open("username.txt");

    let mut f = match f {
        Ok(file) => file,
        Err(e) => return Err(e),
    };

    let mut s = String::new();

    match f.read_to_string(&mut s) {
        Ok(_) => Ok(s),
        Err(e) => Err(e),
    }
}
```

> Note: this code could be made simpler with a single call to
> [`std::fs::read_to_string`](https://doc.rust-lang.org/stable/std/fs/fn.read_to_string.html),
> but we're writing it all out manually here to have an example with multiple
> errors.

This code has two paths that can fail, opening the file and reading the data
from it. If either of these fail to work, we'd like to return an error from
`read_username_from_file`. Doing so involves `match`ing on the result of the
I/O operations. In simple cases like this though, where we are only
propagating errors up the call stack, the matching is just boilerplate -
seeing it written out, in the same pattern every time, doesn't provide the
reader with a great deal of useful information.

With `?`, the above code looks like this:

```rust
# use std::{io::{self, prelude::*}, fs::File};
fn read_username_from_file() -> Result<String, io::Error> {
    let mut f = File::open("username.txt")?;
    let mut s = String::new();

    f.read_to_string(&mut s)?;

    Ok(s)
}
```

The `?` is shorthand for the entire match statements we wrote earlier. In
other words, `?` applies to a `Result` value, and if it was an `Ok`, it
unwraps it and gives the inner value. If it was an `Err`, it returns from the
function you're currently in. Visually, it is much more straightforward.
Instead of an entire match statement, now we are just using the single "?"
character to indicate that here we are handling errors in the standard way,
by passing them up the call stack.

Seasoned Rustaceans may recognize that this is the same as the `try!` macro
that's been available since Rust `1.0`. And indeed, they are the same.
Previously, `read_username_from_file` could have been implemented like this:

```rust
# use std::{io::{self, prelude::*}, fs::File};
fn read_username_from_file() -> Result<String, io::Error> {
    let mut f = try!(File::open("username.txt"));
    let mut s = String::new();

    try!(f.read_to_string(&mut s));

    Ok(s)
}
```

So why extend the language when we already have a macro? There are multiple
reasons. First, `try!` has proved to be extremely useful, and is used often
in idiomatic Rust. It is used so often that we think it's worth having a
sweet syntax. This sort of evolution is one of the great advantages of a
powerful macro system: speculative extensions to the language syntax can be
prototyped and iterated on without modifying the language itself, and in
return, macros that turn out to be especially useful can indicate missing
language features. This evolution, from `try!` to `?` is a great example.

One of the reasons `try!` needs a sweeter syntax is that it is quite
unattractive when multiple invocations of `try!` are used in succession.
Consider:

```rust,ignore
try!(try!(try!(foo()).bar()).baz())
```

as opposed to

```rust,ignore
foo()?.bar()?.baz()?
```

The first is quite difficult to scan visually, and each layer of error
handling prefixes the expression with an additional call to `try!`. This
brings undue attention to the trivial error propagation, obscuring the main
code path, in this example the calls to `foo`, `bar` and `baz`. This sort of
method chaining with error handling occurs in situations like the builder
pattern.

Finally, the dedicated syntax will make it easier in the future to produce
nicer error messages tailored specifically to `?`, whereas it is difficult to
produce nice errors for macro-expanded code generally.

You can use `?` with `Result<T, E>`s, but also with `Option<T>`. In that
case, `?` will return a value for `Some(T)` and return `None` for `None`. One
current restriction is that you cannot use `?` for both in the same function,
as the return type needs to match the type you use `?` on. In the future,
this restriction will be lifted.
