# Simpler lifetimes in `static` and `const`

![Minimum Rust version: 1.17](https://img.shields.io/badge/Minimum%20Rust%20Version-1.17-brightgreen.svg)

In older Rust, you had to explicitly write the `'static` lifetime in any
`static` or `const` that needed a lifetime:

```rust
# mod foo {
const NAME: &'static str = "Ferris";
# }
# mod bar {
static NAME: &'static str = "Ferris";
# }
```

But `'static` is the only possible lifetime there. So Rust now assumes the `'static` lifetime,
and you don't have to write it out:

```rust
# mod foo {
const NAME: &str = "Ferris";
# }
# mod bar {
static NAME: &str = "Ferris";
# }
```

In some situations, this can remove a *lot* of boilerplate:

```rust
# mod foo {
// old
const NAMES: &'static [&'static str; 2] = &["Ferris", "Bors"];
# }
# mod bar {

// new
const NAMES: &[&str; 2] = &["Ferris", "Bors"];
# }
```
