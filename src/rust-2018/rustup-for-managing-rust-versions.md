# Rustup for managing Rust versions

![Minimum Rust version: various](https://img.shields.io/badge/Minimum%20Rust%20Version-various-brightgreen.svg) (this tool has its own versioning scheme and works with all Rust versions)

The [Rustup](https://rustup.rs/) tool has become *the* recommended way to
install Rust, and is advertised on our website. Its powers go further than
that though, allowing you to manage various versions, components, and
platforms.

## For installing Rust

To install Rust through Rustup, you can go to
<https://www.rust-lang.org/install.html>, which will let you know how to do
so on your platform. This will install both `rustup` itself and the `stable`
version of `rustc` and `cargo`.

To install a specific Rust version, you can use `rustup install`:

```console
$ rustup install 1.30.0
```

This works for a specific nightly, as well:

```console
$ rustup install nightly-2018-08-01
```

As well as any of our release channels:

```console
$ rustup install stable
$ rustup install beta
$ rustup install nightly
```

## For updating your installation

To update all of the various channels you may have installed:

```console
$ rustup update
```

This will look at everything you've installed, and if there are new releases,
will update anything that has one.

## Managing versions

To set the default toolchain to something other than `stable`:

```console
$ rustup default nightly
```

To use a toolchain other than the default, use `rustup run`:

```console
$ rustup run nightly cargo build
```

There's also an alias for this that's a little shorter:

```console
$ cargo +nightly build
```

If you'd like to have a different default per-directory, that's easy too!
If you run this inside of a project:

```console
$ rustup override set nightly
```

Or, if you'd like to target a different version of Rust:
```console
$ rustup override set 1.30.0
```

Then when you're in that directory, any invocations of `rustc` or `cargo`
will use that toolchain. To share this with others, you can create a
`rust-toolchain` file with the contents of a toolchain, and check it into
source control. Now, when someone clones your project, they'll get the
right version without needing to `override set` themselves.

## Installing other targets

Rust supports cross-compiling to other targets, and Rustup can help you
manage them. For example, to use MUSL:

```console
$ rustup target add x86_64-unknown-linux-musl
```

And then you can

```console
$ cargo build --target=x86_64-unknown-linux-musl
```

To see the full list of targets you can install:

```console
$ rustup target list
```

## Installing components

Components are used to install certain kinds of tools. While `cargo-install`
has you covered for most tools, some tools need deep integration into the
compiler. Rustup knows exactly what version of the compiler you're using, and
so it's got just the information that these tools need.

Components are per-toolchain, so if you want them to be available to more
than one toolchain, you'll need to install them multiple times. In the
following examples, add a `--toolchain` flag, set to the toolchain you
want to install for, `nightly` for example. Without this flag, it will
install the component for the default toolchain.

To see the full list of components you can install:

```console
$ rustup component list
```

Next, let's talk about some popular components and when you might want to
install them.

### `rust-docs`, for local documentation

This first component is installed by default when you install a toolchain. It
contains a copy of Rust's documentation, so that you can read it offline.

This component cannot be removed for now; if that's of interest, please
comment on [this
issue](https://github.com/rust-lang-nursery/rustup.rs/issues/998).

### `rust-src` for a copy of Rust's source code

The `rust-src` component can give you a local copy of Rust's source code. Why
might you need this? Well, autocompletion tools like Racer use this
information to know more about the functions you're trying to call.

```console
$ rustup component add rust-src
```

### The "preview" components

There are several components in a "preview" stage. These components currently
have `-preview` in their name, and this indicates that they're not quite 100%
ready for general consumption yet. Please try them out and give us feedback,
but know that they do not follow Rust's stability guarantees, and are still
actively changing, possibly in backwards-incompatible ways.

#### `rustfmt-preview` for automatic code formatting

![Minimum Rust version: 1.24](https://img.shields.io/badge/Minimum%20Rust%20Version-1.24-brightgreen.svg)

If you'd like to have your code automatically formatted, you can
install this component:

```console
$ rustup component add rustfmt-preview
```

This will install two tools, `rustfmt` and `cargo-fmt`, that will reformat your
code for you! For example:

```console
$ cargo fmt
```

will reformat your entire Cargo project.

#### `rls-preview` for IDE integration

![Minimum Rust version: 1.21](https://img.shields.io/badge/Minimum%20Rust%20Version-1.21-brightgreen.svg)

Many IDE features are built off of the [`langserver`
protocol](http://langserver.org/). To gain support for Rust with these IDEs,
you'll need to install the Rust language sever, aka the "RLS":

```console
$ rustup component add rls-preview
```

Your IDE should take it from there.

#### `clippy-preview` for more lints

For even more lints to help you write Rust code, you can install `clippy`:

```console
$ rustup component add clippy-preview
```

This will install `cargo-clippy` for you:

```console
$ cargo clippy
```

For more, check out [clippy's
documentation](https://github.com/rust-lang-nursery/rust-clippy).

#### `llvm-tools-preview` for using extra LLVM tools

If you'd like to use the `lld` linker, or other tools like `llvm-objdump` or
`llvm-objcopy`, you can install this component:

```console
$ rustup component add llvm-tools-preview
```

This is the newest component, and so doesn't have good documentation at the
moment.
