# WebAssembly support

![Minimum Rust version: 1.14](https://img.shields.io/badge/Minimum%20Rust%20Version-1.14-brightgreen.svg) for `emscripten`

![Minimum Rust version: nightly](https://img.shields.io/badge/Minimum%20Rust%20Version-nightly-red.svg) for `wasm32-unknown-unknown`

Rust has gained support for [WebAssembly](https://webassembly.org/), meaning
that you can run Rust code in your browser, client-side.

In Rust 1.14, we gained support through
[emscripten](http://kripken.github.io/emscripten-site/index.html). With it
installed, you can write Rust code and have it produce
[asm.js](http://asmjs.org/) (the precusor to wasm) and/or WebAssembly.

Here's an example of using this support:

```console
$ rustup target add wasm32-unknown-emscripten
$ echo 'fn main() { println!("Hello, Emscripten!"); }' > hello.rs
$ rustc --target=wasm32-unknown-emscripten hello.rs
$ node hello.js
```

However, in the meantime, Rust has also grown its own support, independent
from Emscripten. This is known as "the unknown target", because instead of
`wasm32-unknown-emscripten`, it's `wasm32-unknown-unknown`. This will be
the preferred target to use once it's ready, but for now, it's really
only well-supported in nightly.