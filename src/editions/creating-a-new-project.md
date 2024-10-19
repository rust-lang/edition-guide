# Creating a new project

A new project created with Cargo is configured to use the latest edition by
default:

```console
$ cargo new foo
    Creating binary (application) `foo` package
note: see more `Cargo.toml` keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html
$ cat foo/Cargo.toml
[package]
name = "foo"
version = "0.1.0"
edition = "2021"

[dependencies]
```

That `edition = "2021"` setting configures your package to be built using the
Rust 2021 edition. No further configuration needed!

You can use the `--edition <YEAR>` option of `cargo new` to create the project
using some specific edition. For example, creating a new project to use the
Rust 2018 edition could be done like this:

```console
$ cargo new --edition 2018 foo
    Creating binary (application) `foo` package
note: see more `Cargo.toml` keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html
$ cat foo/Cargo.toml
[package]
name = "foo"
version = "0.1.0"
edition = "2018"

[dependencies]
```

Don't worry about accidentally using an invalid year for the edition; the
`cargo new` invocation will not accept an invalid edition year value:

```console
$ cargo new --edition 2019 foo
error: invalid value '2019' for '--edition <YEAR>'
  [possible values: 2015, 2018, 2021, 2024]

  tip: a similar value exists: '2021'

For more information, try '--help'.
```

You can change the value of the `edition` key by simply editing the
`Cargo.toml` file. For example, to cause your package to be built using the
Rust 2015 edition, you would set the key as in the following example:

```toml
[package]
name = "foo"
version = "0.1.0"
edition = "2015"

[dependencies]
```
