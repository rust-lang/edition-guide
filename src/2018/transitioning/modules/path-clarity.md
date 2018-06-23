# Path clarity

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
* Absolute paths begin with a crate name, where the keyword `crate`
  refers to the current crate.
* The `crate` keyword also acts as a visibility modifier, equivalent to today's `pub(crate)`.
* A `foo.rs` and `foo/` subdirectory may coexist; `mod.rs` is no longer needed
  when placing submodules in a subdirectory.

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

One other use for `extern crate` was to import macros; that's no longer needed.
Check [the macro section](2018/transitioning/modules/macros.html) for more.

### Absolute paths begin with `crate` or the crate name

In Rust 2018, paths in `use` statements *must* begin with one of:

- A crate name
- `crate` for the current crate's root
- `self` for the current module's root
- `super` for the current module's parent

Code that looked like this:

```rust,ignore
// Rust 2015

extern crate futures;

use futures::Future;

mod foo {
    struct Bar;
}

use foo::Bar;
```

Now looks like this:

```rust,ignore
// Rust 2018

// 'futures' is the name of a crate
use futures::Future;

mod foo {
    struct Bar;
}

// 'crate' means the current crate
use crate::foo::Bar;
```

In addition, all of these path forms are available outside of `use` statements
as well, which eliminates many sources of confusion. Consider this code in Rust
2015:

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

In the `futures` example, the `my_poll` function signature is incorrect, because `submodule`
contains no items named `futures`; that is, this path is considered relative. But because
`use` is absolute, `use futures::` works even though a lone `futures::` doesn't! With `std`
it can be even more confusing, as you never wrote the `extern crate std;` line at all. So
why does it work in `main` but not in a submodule? Same thing: it's a relative path because
it's not in a `use` declaration. `extern crate std;` is inserted at the crate root, so
it's fine in `main`, but it doesn't exist in the submodule at all.

Let's look at how this change affects things:

```rust,ignore
// Rust 2018

// no more `extern crate futures;`

mod submodule {
    // 'futures' is the name of a crate, so this is absolute and works
    use futures::Future;

    // 'futures' is the name of a crate, so this is absolute and works
    fn my_poll() -> futures::Poll { ... }
}

fn main() {
    // 'std' is the name of a crate, so this is absolute and works
    let five = std::sync::Arc::new(5);
}

mod submodule {
    fn function() {
        // 'std' is the name of a crate, so this is absolute and works
        let five = std::sync::Arc::new(5);
    }
}
```

Much more straightforward.

### The `crate` visibility modifier

In Rust 2015, you can use `pub(crate)` to make something visible
to your entire crate, but not exported publicly:

```rust
mod foo {
    pub mod bar {
        pub(crate) struct Foo;
    }
}

use foo::bar::Foo;
# fn main() {}
```

In Rust 2018, the `crate` keyword is a synonym for this:

```rust,ignore
mod foo {
    pub mod bar {
        crate struct Foo;
    }
}

use foo::bar::Foo;
#fn main() {}
```

This is a minor bit of syntax sugar, but makes using it feel much more
natural.

### No more `mod.rs`

In Rust 2015, if you have a submodule:

```rust,ignore
mod foo;
```

It can live in `foo.rs` or `foo/mod.rs`. If it has submodules of its own, it
*must* be `foo/mod.rs`. So a `bar` submodule of `foo` would live at
`foo/bar.rs`.

In Rust 2018, `mod.rs` is no longer needed. `foo.rs` can just be `foo.rs`,
and the submodule is still `foo/bar.rs`. This eliminates the special
name, and if you have a bunch of files open in your editor, you can clearly
see their names, instead of having a bunch of tabs named `mod.rs`.
