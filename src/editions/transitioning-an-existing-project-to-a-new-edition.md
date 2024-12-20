# Transitioning an existing project to a new edition

Rust includes tooling to automatically transition a project from one edition to the next.
It will update your source code so that it is compatible with the next edition.
Briefly, the steps to update to the next edition are:

1. Run `cargo update` to update your dependencies to the latest versions.
2. Run `cargo fix --edition`
3. Edit `Cargo.toml` and set the `edition` field to the next edition, for example `edition = "2024"`
4. Run `cargo build` or `cargo test` to verify the fixes worked.
5. Run `cargo fmt` to reformat your project.

The following sections dig into the details of these steps, and some of the issues you may encounter along the way.

> It's our intention that the migration to new editions is as smooth an
> experience as possible. If it's difficult for you to upgrade to the latest edition,
> we consider that a bug. If you run into problems with this process, please
> [file a bug report](https://github.com/rust-lang/rust/issues/new/choose). Thank you!

## Starting the migration

As an example, let's take a look at transitioning from the 2015 edition to the 2018 edition.
The steps are essentially the same when transitioning to other editions like 2021.

Imagine we have a crate that has this code in `src/lib.rs`:

```rust
trait Foo {
    fn foo(&self, i32);
}
```

This code uses an anonymous parameter, that `i32`. This is [not
supported in Rust 2018](../rust-2018/trait-system/no-anon-params.md), and
so this would fail to compile. Let's get this code up to date!

## Updating your dependencies

Before we get started, it is recommended to update your dependencies. Some dependencies, particularly some proc-macros or dependencies that do build-time code generation, may have compatibility issues with newer editions. New releases may have been made since you last updated which may fix these issues. Run the following:

```console
cargo update
```

After updating, you may want to run your tests to verify everything is working. If you are using a source control tool such as `git`, you may want to commit these changes separately to keep a logical separation of commits.

## Updating your code to be compatible with the new edition

Your code may or may not use features that are incompatible with the new edition.
In order to help transition to the next edition, Cargo includes the [`cargo fix`] subcommand to automatically update your source code.
To start, let's run it:

```console
cargo fix --edition
```

This will check your code, and automatically fix any issues that it can.
Let's look at `src/lib.rs` again:

```rust
trait Foo {
    fn foo(&self, _: i32);
}
```

It's re-written our code to introduce a parameter name for that `i32` value.
In this case, since it had no name, `cargo fix` will replace it with `_`,
which is conventional for unused variables.

`cargo fix` can't always fix your code automatically.
If `cargo fix` can't fix something, it will print the warning that it cannot fix
to the console. If you see one of these warnings, you'll have to update your code manually.
See the [Advanced migration strategies] chapter for more on working with the migration process, and read the chapters in this guide which explain which changes are needed.
If you have problems, please seek help at the [user's forums](https://users.rust-lang.org/).

## Enabling the new edition to use new features

In order to use some new features, you must explicitly opt in to the new
edition. Once you're ready to continue, change your `Cargo.toml` to add the new
`edition` key/value pair. For example:

```toml
[package]
name = "foo"
version = "0.1.0"
edition = "2018"
```

If there's no `edition` key, Cargo will default to Rust 2015. But in this case,
we've chosen `2018`, and so our code will compile with Rust 2018!

## Testing your code in the new edition

The next step is to test your project on the new edition.
Run your project tests to verify that everything still works, such as running [`cargo test`].
If new warnings are issued, you may want to consider running `cargo fix` again (without the `--edition` flag) to apply any suggestions given by the compiler.

At this point, you may still need to do some manual changes. For example, the automatic migration does not update doctests, and build-time code generation or macros may need manual updating. See the [advanced migrations chapter] for more information.

Congrats! Your code is now valid in both Rust 2015 and Rust 2018!

[advanced migrations chapter]: advanced-migrations.md

## Reformatting with rustfmt

If you use [rustfmt] to automatically maintain formatting within your project, then you should consider reformatting using the new formatting rules of the new edition.

Before reformatting, if you are using a source control tool such as `git`, you may want to commit all the changes you have made up to this point before taking this step. It can be useful to put formatting changes in a separate commit, because then you can see which changes are just formatting versus other code changes, and also possibly ignore the formatting changes in `git blame`.

```console
cargo fmt
```

See the [style editions chapter] for more information.

[rustfmt]: https://github.com/rust-lang/rustfmt
[style editions chapter]: ../rust-2024/rustfmt-style-edition.md

## Migrating to an unstable edition

After an edition is released, there is roughly a three year window before the next edition.
During that window, new features may be added to the next edition, which will only be available on the [nightly channel].
If you want to help test those new features before they are stabilized, you can use the nightly channel to try them out.

The steps are roughly similar to the stable channel:

1. Install the most recent nightly: `rustup update nightly`.
2. Run `cargo +nightly fix --edition`.
3. Edit `Cargo.toml` and place `cargo-features = ["edition20xx"]` at the top (above `[package]`), and change the edition field to say `edition = "20xx"` where `20xx` is the edition you are upgrading to.
4. Run `cargo +nightly check` to verify it now works in the new edition.

> **⚠ Caution**: Features implemented in the next edition may not have automatic migrations implemented with `cargo fix`, and the features themselves may not be finished.
> When possible, this guide should contain information about which features are implemented
> on nightly along with more information about their status.
> A few months before the edition is stabilized, all of the new features should be fully implemented, and the [Rust Blog] will announce a call for testing.

[`cargo fix`]: ../../cargo/commands/cargo-fix.html
[`cargo test`]: ../../cargo/commands/cargo-test.html
[Advanced migration strategies]: advanced-migrations.md
[nightly channel]: ../../book/appendix-07-nightly-rust.html
[Rust Blog]: https://blog.rust-lang.org/
