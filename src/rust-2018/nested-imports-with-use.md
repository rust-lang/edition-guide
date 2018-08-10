# Nested imports with `use`

![Minimum Rust version: 1.25](https://img.shields.io/badge/Minimum%20Rust%20Version-1.25-brightgreen.svg)

A new way to write `use` statements has been added to Rust: nested import
groups. If you’ve ever written a set of imports like this:

```rust
use std::fs::File;
use std::io::Read;
use std::path::{Path, PathBuf};
```

You can now write this:

```rust
# mod foo {
// on one line
use std::{fs::File, io::Read, path::{Path, PathBuf}};
# }

# mod bar {
// with some more breathing room
use std::{
    fs::File,
    io::Read,
    path::{
        Path,
        PathBuf
    }
};
# }
```

This can reduce some repetition, and make things a bit more clear.
