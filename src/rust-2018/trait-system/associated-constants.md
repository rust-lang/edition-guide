# Associated constants

![Minimum Rust version: 1.20](https://img.shields.io/badge/Minimum%20Rust%20Version-1.20-brightgreen.svg)

You can define traits, structs, and enums that have “associated functions”:

```rust
struct Struct;

impl Struct {
    fn foo() {
        println!("foo is an associated function of Struct");
    }
}

fn main() {
    Struct::foo();
}
```

These are called “associated functions” because they are functions that are
associated with the type, that is, they’re attached to the type itself, and
not any particular instance.

Rust 1.20 adds the ability to define “associated constants” as well:

```rust
struct Struct;

impl Struct {
    const ID: u32 = 0;
}

fn main() {
    println!("the ID of Struct is: {}", Struct::ID);
}
```

That is, the constant `ID` is associated with `Struct`. Like functions,
associated constants work with traits and enums as well.

Traits have an extra ability with associated constants that gives them some
extra power. With a trait, you can use an associated constant in the same way
you’d use an associated type: by declaring it, but not giving it a value. The
implementor of the trait then declares its value upon implementation:

```rust
trait Trait {
    const ID: u32;
}

struct Struct;

impl Trait for Struct {
    const ID: u32 = 5;
}

fn main() {
    println!("{}", Struct::ID);
}
```

Before this feature, if you wanted to make a trait that represented floating
point numbers, you’d have to write this:

```rust
trait Float {
    fn nan() -> Self;
    fn infinity() -> Self;
    // ...
}
```

This is slightly unwieldy, but more importantly, because they’re functions,
they cannot be used in constant expressions, even though they only return a
constant. Because of this, a design for `Float` would also have to include
constants as well:

```rust,ignore
mod f32 {
    const NAN: f32 = 0.0f32 / 0.0f32;
    const INFINITY: f32 = 1.0f32 / 0.0f32;

    impl Float for f32 {
        fn nan() -> Self {
            f32::NAN
        }
        fn infinity() -> Self {
            f32::INFINITY
        }
    }
}
```

Associated constants let you do this in a much cleaner way. This trait
definition:

```rust
trait Float {
    const NAN: Self;
    const INFINITY: Self;
    // ...
}
```

Leads to this implementation:

```rust,ignore
mod f32 {
    impl Float for f32 {
        const NAN: f32 = 0.0f32 / 0.0f32;
        const INFINITY: f32 = 1.0f32 / 0.0f32;
    }
}
```

much cleaner, and more versatile.
