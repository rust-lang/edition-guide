# Simpler lifetimes in static and const

![Minimum Rust version: 1.17](https://img.shields.io/badge/Minimum%20Rust%20Version-1.17-brightgreen.svg)

In older Rust, you had to explicitly write the `'static` lifetime in any
`static` or `const` that needed a lifetime:

```rust
const NAME: &'static str = "Ferris";
static NAME: &'static str = "Ferris";
```

But `'static` is the only possible lifetime there. So Rust now assumes the `'static` lifetime,
and you don't have to write it out:

```rust
const NAME: &str = "Ferris";
static NAME: &str = "Ferris";
```

In some situations, this can remove a *lot* of boilerplate:

```rust
// old
const NAMES: &'static [&'static str; 2] = &["Ferris", "Bors"];

// new
const NAMES: &[&str; 2] = &["Ferris", "Bors"];
```