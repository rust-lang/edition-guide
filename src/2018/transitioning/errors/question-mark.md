# `?` in `fn main()` and `#[test]`s

Typically in Rust, error handling revolves around returning `Result` and using
`?` to propagate errors. For those who write many small programs and, hopefully,
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

To solve this in edition 2015, you might have written something like:

```rust
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
`main` is just boilerplate. When you write many `#[test]`s, this can become
a problem.

As of [1.26](main-can-return-a-result), you can instead let your `#[test]`s and `main` functions return
a `Result`.

[main-can-return-a-result]: https://blog.rust-lang.org/2018/05/10/Rust-1.26.html#main-can-return-a-result

```rust,no_run
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

Getting `-> Result<..>` to work in the context of `main` and `#[test]`s is no
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
