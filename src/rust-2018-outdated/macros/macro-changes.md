# Macro changes

![Minimum Rust version: 1.31](https://img.shields.io/badge/Minimum%20Rust%20Version-1.31-brightgreen.svg)

## `macro_rules!` style macros

In Rust 2018, you can import specific macros from external crates via `use`
statements, rather than the old `#[macro_use]` attribute.

For example, consider a `bar` crate that implements a `baz!` macro. In
`src/lib.rs`:

```rust
#[macro_export]
macro_rules! baz {
    () => ()
}
```

In your crate, you would have written

```rust,ignore
// Rust 2015

#[macro_use]
extern crate bar;

fn main() {
    baz!();
}
```

Now, you write:

```rust,ignore
// Rust 2018

use bar::baz;

fn main() {
    baz!();
}
```

This moves `macro_rules` macros to be a bit closer to other kinds of items.

Note that you'll still need `#[macro_use]` to use macros you've defined
in your own crate; this feature only works for importing macros from
external crates.

## Procedural macros

When using procedural macros to derive traits, you will have to name the macro
that provides the custom derive. This generally matches the name of the trait,
but check with the documentation of the crate providing the derives to be sure.

For example, with Serde you would have written

```rust,ignore
// Rust 2015
extern crate serde;
#[macro_use] extern crate serde_derive;

#[derive(Serialize, Deserialize)]
struct Bar;
```

Now, you write instead:

```rust,ignore
// Rust 2018
use serde_derive::{Serialize, Deserialize};

#[derive(Serialize, Deserialize)]
struct Bar;
```


## More details

This only works for macros defined in external crates.
For macros defined locally, `#[macro_use] mod foo;` is still required, as it was in Rust 2015.

### Local helper macros

Sometimes it is helpful or necessary to have helper macros inside your module. This can make
supporting both versions of rust more complicated.

For example, let's make a simplified (and slightly contrived) version of the `log` crate in 2015
edition style:

```rust
use std::fmt;

/// How important/severe the log message is.
#[derive(Copy, Clone)]
pub enum LogLevel {
    Warn,
    Error
}

impl fmt::Display for LogLevel {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            LogLevel::Warn => write!(f, "warning"),
            LogLevel::Error => write!(f, "error"),
        }
    }
}

// A helper macro to log the message.
#[doc(hidden)]
#[macro_export]
macro_rules! __impl_log {
    ($level:expr, $msg:expr) => {{
        println!("{}: {}", $level, $msg)
    }}
}

/// Warn level log message
#[macro_export]
macro_rules! warn {
    ($($args:tt)*) => {
        __impl_log!($crate::LogLevel::Warn, format_args!($($args)*))
    }
}

/// Error level log message
#[macro_export]
macro_rules! error {
    ($($args:tt)*) => {
        __impl_log!($crate::LogLevel::Error, format_args!($($args)*))
    }
}
```

Our `__impl_log!` macro is private to our module, but needs to be exported as it is called by other
macros, and in 2015 edition all used macros must be exported.

Now, in 2018 this example will not compile:

```rust,ignore
use log::error;

fn main() {
    error!("error message");
}
```

will give an error message about not finding the `__impl_log!` macro. This is because unlike in 
the 2015 edition, macros are namespaced and we must import them. We could do

```rust,ignore
use log::{__impl_log, error};
```

which would make our code compile, but `__impl_log` is meant to be an implementation detail!

#### Macros with `$crate::` prefix.

The cleanest way to handle this situation is to use the `$crate::` prefix for macros, the same as
you would for any other path. Versions of the compiler >= 1.30 will handle this in both editions:

```rust
macro_rules! warn {
    ($($args:tt)*) => {
        $crate::__impl_log!($crate::LogLevel::Warn, format_args!($($args)*))
    }
}

// ...
```

However, this will not work for older versions of the compiler that don't understand the
`$crate::` prefix for macros.

#### Macros using `local_inner_macros`

We also have the `local_inner_macros` modifier that we can add to our `#[macro_export]` attribute.
This has the advantage of working with older rustc versions (older versions just ignore the extra
modifier). The downside is that it's a bit messier:

```rust,ignore
#[macro_export(local_inner_macros)]
macro_rules! warn {
    ($($args:tt)*) => {
        __impl_log!($crate::LogLevel::Warn, format_args!($($args)*))
    }
}
```

So the code knows to look for any macros used locally. But wait - this won't compile, because we
use the `format_args!` macro that isn't in our local crate (hence the convoluted example). The
solution is to add a level of indirection: we create a macro that wraps `format_args`, but is local 
to our crate. That way everything works in both editions (sadly we have to pollute the global
namespace a bit, but that's ok).

```rust
// I've used the pattern `_<my crate  name>__<macro name>` to name this macro, hopefully avoiding
// name clashes.
#[doc(hidden)]
#[macro_export]
macro_rules! _log__format_args {
    ($($inner:tt)*) => {
        format_args! { $($inner)* }
    }
}
```

Here we're using the most general macro pattern possible, a list of token trees. We just pass
whatever tokens we get to the inner macro, and rely on it to report errors.

So the full 2015/2018 working example would be:

```rust
use std::fmt;

/// How important/severe the log message is.
#[derive(Debug, Copy, Clone)]
pub enum LogLevel {
    Warn,
    Error
}

impl fmt::Display for LogLevel {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            LogLevel::Warn => write!(f, "warning"),
            LogLevel::Error => write!(f, "error"),
        }
    }
}

// A helper macro to log the message.
#[doc(hidden)]
#[macro_export]
macro_rules! __impl_log {
    ($level:expr, $msg:expr) => {{
        println!("{}: {}", $level, $msg)
    }}
}

/// Warn level log message
#[macro_export(local_inner_macros)]
macro_rules! warn {
    ($($args:tt)*) => {
        __impl_log!($crate::LogLevel::Warn, _log__format_args!($($args)*))
    }
}

/// Error level log message
#[macro_export(local_inner_macros)]
macro_rules! error {
    ($($args:tt)*) => {
        __impl_log!($crate::LogLevel::Error, _log__format_args!($($args)*))
    }
}

#[doc(hidden)]
#[macro_export]
macro_rules! _log__format_args {
    ($($inner:tt)*) => {
        format_args! { $($inner)* }
    }
}
```

Once everyone is using a rustc version >= 1.30, we can all just use the `$crate::` method (2015
crates are guaranteed to carry on compiling fine with later versions of the compiler). We need to
wait for package managers and larger organisations to update their compilers before this happens,
so in the mean time we can use the `local_inner_macros` method to support everybody. :)
