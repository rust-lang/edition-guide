# Field init shorthand

![Minimum Rust version: 1.17](https://img.shields.io/badge/Minimum%20Rust%20Version-1.17-brightgreen.svg)

In older Rust, when initializing a struct, you must always give the full set of `key: value` pairs
for its fields:

```rust
struct Point {
    x: i32,
    y: i32,
}

let a = 5;
let b = 6;

let p = Point {
    x: a,
    y: b,
};
```

However, often these variables would have the same names as the fields. So you'd end up
with code that looks like this:

```rust,ignore
let p = Point {
    x: x,
    y: y,
};
```

Now, if the variable is of the same name, you don't have to write out both, just write out the key:

```rust
struct Point {
    x: i32,
    y: i32,
}

let x = 5;
let y = 6;

// new
let p = Point {
    x,
    y,
};
```