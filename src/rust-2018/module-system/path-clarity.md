# Path clarity

![Minimum Rust version: beta](https://img.shields.io/badge/Minimum%20Rust%20Version-beta-orange.svg)
![Minimum Rust version: nightly](https://img.shields.io/badge/Minimum%20Rust%20Version-nightly-red.svg) for "uniform paths"

The module system is often one of the hardest things for people new to Rust. Everyone
has their own things that take time to master, of course, but there's a root
cause for why it's so confusing to many: while there are simple and
consistent rules defining the module system, their consequences can feel
inconsistent, counterintuitive and mysterious.

As such, the 2018 edition of Rust introduces a few new module system
features, but they end up *simplifying* the module system, to make it more
clear as to what is going on.

Here's a brief summary:

* `extern crate` is no longer needed
* The `crate` keyword refers to the current crate.
* Absolute paths begin with a crate name, where the keyword `crate`
  refers to the current crate.
* A `foo.rs` and `foo/` subdirectory may coexist; `mod.rs` is no longer needed
  when placing submodules in a subdirectory.

These may seem like arbitrary new rules when put this way, but the mental
model is now significantly simplified overall. Read on for more details!

> Additionally, in nightly, there's an additional possible tweak to paths
> called "Uniform paths". This is backwards compatible with the new path
> changes. Uniform paths have a dedicated section at the end of this guide.

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

One other use for `extern crate` was to import macros; that's no longer needed.
Check [the macro section](../macros/macro-changes.html) for more.

If you've been using `as` to rename your crate like this:

```rust,ignore
extern crate futures as f;

use f::Future;
```

then removing the `extern crate` line on its own won't work. You'll need to do this:

```rust,ignore
use futures as f;

use f::Future;
```

This change will need to happen in any module that uses `f`.

### The `crate` keyword refers to the current crate.

In `use` declarations and in other code, you can refer to the root of the
current crate with the `crate::` prefix. For instance, `crate::foo::bar` will
always refer to the name `bar` inside the module `foo`, from anywhere else in
the same crate.

The prefix `::` previously referred to either the crate root or an external
crate; it now unambiguously refers to an external crate. For instance,
`::foo::bar` always refers to the name `bar` inside the external crate `foo`.

### Changes to paths

In Rust 2018, paths in `use` declarations *must* begin with a crate name,
`crate`, `self`, or `super`.

Code that looked like this:

```rust,ignore
// Rust 2015

extern crate futures;

use futures::Future;

mod foo {
    pub struct Bar;
}

use foo::Bar;
```

Now looks like this:

```rust,ignore
// Rust 2018

// 'futures' is the name of a crate
use futures::Future;

mod foo {
    pub struct Bar;
}

// 'crate' means the current crate
use crate::foo::Bar;
```

In addition, all of these path forms are available outside of `use`
declarations as well, which eliminates many sources of confusion. Consider
this code in Rust 2015:

```rust,ignore
// Rust 2015

extern crate futures;

mod submodule {
    // this works!
    use futures::Future;

    // so why doesn't this work?
    fn my_poll() -> futures::Poll { ... }
}

fn main() {
    // this works
    let five = std::sync::Arc::new(5);
}

mod submodule {
    fn function() {
        // ... so why doesn't this work
        let five = std::sync::Arc::new(5);
    }
}
```

> In real code, you couldn't repeat `mod submodule`, and `function` would be defined
> in the first `mod` block.

In the `futures` example, the `my_poll` function signature is incorrect,
because `submodule` contains no items named `futures`; that is, this path is
considered relative. `use futures::` works even though a lone `futures::`
doesn't! With `std` it can be even more confusing, as you never wrote the
`extern crate std;` line at all. So why does it work in `main` but not in a
submodule? Same thing: it's a relative path because it's not in a `use`
declaration. `extern crate std;` is inserted at the crate root, so it's fine
in `main`, but it doesn't exist in the submodule at all.

Let's look at how this change affects things:

```rust,ignore
// Rust 2018

// no more `extern crate futures;`

mod submodule {
    // 'futures' is the name of a crate, so this works
    use futures::Future;

    // 'futures' is the name of a crate, so this works
    fn my_poll<T, E>() -> futures::Poll {
        unimplemented!()
    }

    fn function() {
        // 'std' is the name of a crate, so this works
        let five = std::sync::Arc::new(5);
    }
}

fn main() {
    // 'std' is the name of a crate, so this works
    let five = std::sync::Arc::new(5);
}
```

Much more straightforward.

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

# Uniform paths

> Uniform paths are a nightly-only feature.

The uniform paths variant of Rust 2018 simplifies and unifies path handling
compared to Rust 2015. In Rust 2015, paths work differently in `use`
declarations than they do elsewhere. In particular, paths in `use`
declarations would always start from the crate root, while paths in other code
implicitly started from the current module. Those differences didn't have any
effect in the top-level module, which meant that everything would seem
straightforward until working on a project large enough to have submodules.

In the uniform paths variant of Rust 2018, paths in `use` declarations and in
other code always work the same way, both in the top-level module and in any
submodule. You can always use a relative path from the current module, a path
starting from an external crate name, or a path starting with `crate`, `super`,
or `self`.

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
// Rust 2018 (uniform paths variant)

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

With Rust 2018, however, the same code will also work completely unmodified in
a submodule:

```rust,ignore
// Rust 2018 (uniform paths variant)

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
