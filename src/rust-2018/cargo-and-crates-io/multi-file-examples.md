# Multi-file examples

![Minimum Rust version: 1.22](https://img.shields.io/badge/Minimum%20Rust%20Version-1.22-brightgreen.svg)

Cargo has an `examples` feature for showing people how to use your package.
By putting individual files inside of the top-level `examples` directory, you
can create multiple examples.

But what if your example is too big for a single file? Cargo supports adding
sub-directories inside of `examples`, and looks for a `main.rs` inside of
them to build the example. It looks like this:

```text
my-package
 └──src
     └── lib.rs // code here
 └──examples 
     └── simple-example.rs // a single-file example
     └── complex-example
        └── helper.rs
        └── main.rs // a more complex example that also uses `helper` as a submodule
```
