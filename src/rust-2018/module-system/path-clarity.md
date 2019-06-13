# Path clarity

![Minimum Rust version: 1.31](https://img.shields.io/badge/Minimum%20Rust%20Version-1.31-brightgreen.svg)

The module system is often one of the hardest things for people new to Rust. Everyone
has their own things that take time to master, of course, but there's a root
cause for why it's so confusing to many: while there are simple and
consistent rules defining the module system, their consequences can feel
inconsistent, counterintuitive and mysterious.

As such, the 2018 edition of Rust introduces a few new module system
features, but they end up *simplifying* the module system, to make it more
clear as to what is going on.

Here's a brief summary:

* `extern crate` is no longer needed in 99% of circumstances.
* The `crate` keyword refers to the current crate.
* Paths may start with a crate name, even within submodules.
* Paths starting with `::` must reference an external crate.
* A `foo.rs` and `foo/` subdirectory may coexist; `mod.rs` is no longer needed
  when placing submodules in a subdirectory.
* Paths in `use` declarations work the same as other paths.

These may seem like arbitrary new rules when put this way, but the mental
model is now significantly simplified overall. Read on for more details!

## More details

Let's talk about each new feature in turn.

### No more `extern crate`

This one is quite straightforward: you no longer need to write `extern crate` to
import a crate into your project. Before:

```rust,ignore
// Rust 2015

extern crate futures;

mod submodule {
    use futures::Future;
}
```

After:

```rust,ignore
// Rust 2018

mod submodule {
    use futures::Future;
}
```

Now, to add a new crate to your project, you can add it to your `Cargo.toml`,
and then there is no step two. If you're not using Cargo, you already had to pass
`--extern` flags to give `rustc` the location of external crates, so you'd just
keep doing what you were doing there as well.

> One small note here: `cargo fix` will not currently automate this change. We may
> have it do this for you in the future.

#### An exception

There's one exception to this rule, and that's the "sysroot" crates. These are the
crates distributed with Rust itself. We'd eventually like to remove the requirement
for `extern crate` for them as well, but it hasn't shipped yet.

You'll need to use `extern crate` for:

* `proc_macro`

Additionally, you would need to use it for:

* `core`
* `std`

However, `extern crate std;` is already implicit, and with `#![no_std]`,
`extern crate core;` is already implicit. You'll only need these in highly
specialized situations.

Finally, on nightly, you'll need it for crates like:

* `alloc`
* `test`

#### Macros

One other use for `extern crate` was to import macros; that's no longer needed.
Check [the macro section](../macros/macro-changes.md) for more.

#### Renaming crates

If you've been using `as` to rename your crate like this:

```rust,ignore
extern crate futures as f;

use f::Future;
```

then removing the `extern crate` line on its own won't work. You'll need to do this:

```rust,ignore
use futures as f;

use self::f::Future;
```

This change will need to happen in any module that uses `f`.

### The `crate` keyword refers to the current crate

In `use` declarations and in other code, you can refer to the root of the
current crate with the `crate::` prefix. For instance, `crate::foo::bar` will
always refer to the name `bar` inside the module `foo`, from anywhere else in
the same crate.

The prefix `::` previously referred to either the crate root or an external
crate; it now unambiguously refers to an external crate. For instance,
`::foo::bar` always refers to the name `bar` inside the external crate `foo`.

### Extern crate paths

Previously, using an external crate in a module without a `use` import
required a leading `::` on the path.

```rust,ignore
// Rust 2015

extern crate chrono;

fn foo() {
    // this works in the crate root
    let x = chrono::Utc::now();
}

mod submodule {
    fn function() {
        // but in a submodule it requires a leading :: if not imported with `use`
        let x = ::chrono::Utc::now();
    }
}
```

Now, extern crate names are in scope in the entire crate, including
submodules.

```rust,ignore
// Rust 2018

fn foo() {
    // this works in the crate root
    let x = chrono::Utc::now();
}

mod submodule {
    fn function() {
        // crates may be referenced directly, even in submodules
        let x = chrono::Utc::now();
    }
}
```

### No more `mod.rs`

In Rust 2015, if you have a submodule:

```rust,ignore
///  foo.rs
///  or
///  foo/mod.rs

mod foo;
```

It can live in `foo.rs` or `foo/mod.rs`. If it has submodules of its own, it
*must* be `foo/mod.rs`. So a `bar` submodule of `foo` would live at
`foo/bar.rs`.

In Rust 2018, `mod.rs` is no longer needed.

```rust,ignore
///  foo.rs
///  foo/bar.rs

mod foo;

/// in foo.rs
mod bar;
```

`foo.rs` can just be `foo.rs`,
and the submodule is still `foo/bar.rs`. This eliminates the special
name, and if you have a bunch of files open in your editor, you can clearly
see their names, instead of having a bunch of tabs named `mod.rs`.

### `use` paths

![Minimum Rust version: 1.32](https://img.shields.io/badge/Minimum%20Rust%20Version-1.32-brightgreen.svg)

Rust 2018 simplifies and unifies path handling compared to Rust 2015. In Rust
2015, paths work differently in `use` declarations than they do elsewhere. In
particular, paths in `use` declarations would always start from the crate
root, while paths in other code implicitly started from the current scope.
Those differences didn't have any effect in the top-level module, which meant
that everything would seem straightforward until working on a project large
enough to have submodules.

In Rust 2018, paths in `use` declarations and in other code work the same way,
both in the top-level module and in any submodule. You can use a relative path
from the current scope, a path starting from an external crate name, or a path
starting with `crate`, `super`, or `self`.

Code that looked like this:

```rust,ignore
// Rust 2015

extern crate futures;

use futures::Future;

mod foo {
    pub struct Bar;
}

use foo::Bar;

fn my_poll() -> futures::Poll { ... }

enum SomeEnum {
    V1(usize),
    V2(String),
}

fn func() {
    let five = std::sync::Arc::new(5);
    use SomeEnum::*;
    match ... {
        V1(i) => { ... }
        V2(s) => { ... }
    }
}
```

will look exactly the same in Rust 2018, except that you can delete the `extern
crate` line:

```rust,ignore
// Rust 2018

use futures::Future;

mod foo {
    pub struct Bar;
}

use foo::Bar;

fn my_poll() -> futures::Poll { ... }

enum SomeEnum {
    V1(usize),
    V2(String),
}

fn func() {
    let five = std::sync::Arc::new(5);
    use SomeEnum::*;
    match ... {
        V1(i) => { ... }
        V2(s) => { ... }
    }
}
```

The same code will also work completely unmodified in a submodule:

```rust,ignore
// Rust 2018

mod submodule {
    use futures::Future;

    mod foo {
        pub struct Bar;
    }

    use foo::Bar;

    fn my_poll() -> futures::Poll { ... }

    enum SomeEnum {
        V1(usize),
        V2(String),
    }

    fn func() {
        let five = std::sync::Arc::new(5);
        use SomeEnum::*;
        match ... {
            V1(i) => { ... }
            V2(s) => { ... }
        }
    }
}
```

This makes it easy to move code around in a project, and avoids introducing
additional complexity to multi-module projects.

If a path is ambiguous, such as if you have an external crate and a local
module or item with the same name, you'll get an error, and you'll need to
either rename one of the conflicting names or explicitly disambiguate the path.
To explicitly disambiguate a path, use `::name` for an external crate name, or
`self::name` for a local module or item.
