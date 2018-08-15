# The `?` operator for easier error handling

![Minimum Rust version: 1.13](https://img.shields.io/badge/Minimum%20Rust%20Version-1.13-brightgreen.svg) for `Result<T, E>`

![Minimum Rust version: 1.22](https://img.shields.io/badge/Minimum%20Rust%20Version-1.22-brightgreen.svg) for `Option<T>`

![Minimum Rust version: 1.26](https://img.shields.io/badge/Minimum%20Rust%20Version-1.26-brightgreen.svg) for using `?` in `main` and tests

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
> but we're writing it all out manually here to have an example with mutliple
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
this restriction will be lifed.

## `?` in `main` and tests

Rust's error handling revolves around returning `Result<T, E>` and using `?`
to propagate errors. For those who write many small programs and, hopefully,
many tests, one common paper cut has been mixing entry points such as `main`
and `#[test]`s with error handling.

As an example, you might have tried to write:

```rust,ignore
use std::fs::File;

fn main() {
    let f = File::open("bar.txt")?;
}
```

Since `?` works by propagating the `Result` with an early return to the
enclosing function, the snippet above does not work, and results today
in the following error:

```rust,ignore
error[E0277]: the `?` operator can only be used in a function that returns `Result`
              or `Option` (or another type that implements `std::ops::Try`)
 --> src/main.rs:5:13
  |
5 |     let f = File::open("bar.txt")?;
  |             ^^^^^^^^^^^^^^^^^^^^^^ cannot use the `?` operator in a function that returns `()`
  |
  = help: the trait `std::ops::Try` is not implemented for `()`
  = note: required by `std::ops::Try::from_error`
```

To solve this problem in Rust 2015, you might have written something like:

```rust
// Rust 2015

# use std::process;
# use std::error::Error;

fn run() -> Result<(), Box<Error>> {
    // real logic..
    Ok(())
}

fn main() {
    if let Err(e) = run() {
        println!("Application error: {}", e);
        process::exit(1);
    }
}
```

However, in this case, the `run` function has all the interesting logic and
`main` is just boilerplate. The problem is even worse for `#[test]`s, since
there tend to be a lot more of them.

In Rust 2018 you can instead let your `#[test]`s and `main` functions return
a `Result`:

```rust,no_run
// Rust 2018

use std::fs::File;

fn main() -> Result<(), std::io::Error> {
    let f = File::open("bar.txt")?;

    Ok(())
}
```

In this case, if say the file doesn't exist and there is an `Err(err)` somewhere,
then `main` will exit with an error code (not `0`) and print out a `Debug`
representation of `err`.

## More details

Getting `-> Result<..>` to work in the context of `main` and `#[test]`s is not
magic. It is all backed up by a `Termination` trait which all valid return
types of `main` and testing functions must implement. The trait is defined as:

```rust
pub trait Termination {
    fn report(self) -> i32;
}
```

When setting up the entry point for your application, the compiler will use this
trait and call `.report()` on the `Result` of the `main` function you have written.

Two simplified example implementations of this trait for `Result` and `()` are:

```rust
# #![feature(process_exitcode_placeholder, termination_trait_lib)]
# use std::process::ExitCode;
# use std::fmt;
#
# pub trait Termination { fn report(self) -> i32; }

impl Termination for () {
    fn report(self) -> i32 {
        # use std::process::Termination;
        ExitCode::SUCCESS.report()
    }
}

impl<E: fmt::Debug> Termination for Result<(), E> {
    fn report(self) -> i32 {
        match self {
            Ok(()) => ().report(),
            Err(err) => {
                eprintln!("Error: {:?}", err);
                # use std::process::Termination;
                ExitCode::FAILURE.report()
            }
        }
    }
}
```

As you can see in the case of `()`, a success code is simply returned.
In the case of `Result`, the success case delegates to the implementation for
`()` but prints out an error message and a failure exit code on `Err(..)`.

To learn more about the finer details, consult either [the tracking issue](https://github.com/rust-lang/rust/issues/43301) or [the RFC](https://github.com/rust-lang/rfcs/blob/master/text/1937-ques-in-main.md).
