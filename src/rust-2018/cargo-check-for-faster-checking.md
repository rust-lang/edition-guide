# cargo check for faster checking

![Minimum Rust version: 1.16](https://img.shields.io/badge/Minimum%20Rust%20Version-1.16-brightgreen.svg) for being built-in

![Minimum Rust version: various](https://img.shields.io/badge/Minimum%20Rust%20Version-various-brightgreen.svg) using [this package](https://github.com/rsolomo/cargo-check) for older versions

`cargo check`is a new subcommand should speed up the development
workflow in many cases.

What does it do? Let's take a step back and talk about how `rustc` compiles
your code. Compilation has many "passes", that is, there are many distinct
steps that the compiler takes on the road from your source code to producing
the final binary. However, you can think of this process in two big steps:
first, `rustc` does all of its safety checks, makes sure your syntax is
correct, all that stuff. Second, once it's satisfied that everything is in
order, it produces the actual binary code that you end up executing.

It turns out that that second step takes a lot of time. And most of the time,
it's not neccesary. That is, when you're working on some Rust code, many
developers will get into a workflow like this:

1. Write some code.
2. Run `cargo build` to make sure it compiles.
3. Repeat 1-2 as needed.
4. Run `cargo test` to make sure your tests pass.
5. Try the binary yourself
6. GOTO 1.

In step two, you never actually run your code. You're looking for feedback
from the compiler, not to actually run the binary. `cargo check` supports
exactly this use-case: it runs all of the compiler's checks, but doesn't
produce the final binary. To use it:

```console
$ cargo check
```

where you may normally `cargo build`. The workflow now looks like:

1. Write some code.
2. Run `cargo check` to make sure it compiles.
3. Repeat 1-2 as needed.
4. Run `cargo test` to make sure your tests pass.
5. Run `cargo build` to build a binary and try it yourself
6. GOTO 1.


So how much speedup do you actually get? Like most performance related
questions, the answer is "it depends." Here are some very un-scientific
benchmarks from back when `cargo-check` was first released:

|                 | thanks  | cargo   | diesel |
|-----------------|---------|---------|--------|
| initial build   | 134.75s | 236.78s | 15.27s |
| initial check   | 50.88s  | 148.52s | 12.81s |
| speedup         | 2.648   | 1.594   | 1.192  |
| secondary build | 15.97s  | 64.34s  | 13.54s |
| secondary check | 2.9s    | 9.29s   | 12.3s  |
| speedup         | 5.506   | 6.925   | 1.100  |

The 'initial' categories are the first build after cloning down a project.
The 'secondary' categories involved adding one blank line to the top of
`src\lib.rs` and running the command again. That's why the initial ones are
more dramatic; they involve also doing this for all dependencies, as well as
the crate itself. As you can see, larger projects with many dependencies see
a big improvement, but smaller ones see much more modest gains.