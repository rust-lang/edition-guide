# `const fn`

Initially added: ![Minimum Rust version: 1.31](https://img.shields.io/badge/Minimum%20Rust%20Version-1.31-brightgreen.svg)

Expanded in many releases, see each aspect below for more details.

A `const fn` allows you to execute code in a "const context." For example:

```
const fn five() -> i32 {
    5
}

const FIVE: i32 = five();
```

You cannot execute arbitrary code; the reasons why boil down to "you can
destroy the type system." The details are a bit too much to put here, but the
core idea is that `const fn` started off allowing the absolutely minimal
subset of the language, and has slowly added more abilities over time.
Therefore, while you can create a `const fn` in Rust 1.31, you cannot do much
with it. This is why we didn't add `const fn` to the Rust 2018 section; it
truly didn't become useful until after the release of the 2018 edition. This
means that if you read this document top to bottom, the earlier versions may
describe restrictions that are relaxed in later versions.

Additionally, this has allowed more and more of the standard library to be
made `const`, we won't put all of those changes here, but you should know
that it is becoming more `const` over time.

## Arithmetic and comparison operators on integers

![Minimum Rust version: 1.31](https://img.shields.io/badge/Minimum%20Rust%20Version-1.31-brightgreen.svg)

You can do arithmetic on integer literals:

```
const fn foo() -> i32 {
    5 + 6
}
```

## Many boolean operators

![Minimum Rust version: 1.31](https://img.shields.io/badge/Minimum%20Rust%20Version-1.31-brightgreen.svg)

You can use boolean operators other than `&&` and `||`, because they short-circut evaluation:

```
const fn mask(val: u8) -> u8 {
    let mask = 0x0f;

    mask & val
}
```

## Constructing arrays, structs, enums, and tuples

![Minimum Rust version: 1.31](https://img.shields.io/badge/Minimum%20Rust%20Version-1.31-brightgreen.svg)

You can create arrays, structs, enums, and tuples:

```
struct Point {
    x: i32,
    y: i32,
}

enum Error {
    Incorrect,
    FileNotFound,
}

const fn foo() {
    let array = [1, 2, 3];

    let point = Point {
        x: 5,
        y: 10,
    };

    let error = Error::FileNotFound;

    let tuple = (1, 2, 3);
}
```

## Calls to other const fns

![Minimum Rust version: 1.31](https://img.shields.io/badge/Minimum%20Rust%20Version-1.31-brightgreen.svg)

You can call `const fn` from a `const fn`:


```
const fn foo() -> i32 {
    5
}

const fn bar() -> i32 {
    foo()
}
```

## Index expressions on arrays and slices

![Minimum Rust version: 1.31](https://img.shields.io/badge/Minimum%20Rust%20Version-1.31-brightgreen.svg)

You can index into an array or slice:

```
const fn foo() -> i32 {
    let array = [1, 2, 3];

    array[1]
}
```

## Field accesses on structs and tuples

![Minimum Rust version: 1.31](https://img.shields.io/badge/Minimum%20Rust%20Version-1.31-brightgreen.svg)

You can access parts of a struct or tuple:

```
struct Point {
    x: i32,
    y: i32,
}

const fn foo() {
    let point = Point {
        x: 5,
        y: 10,
    };

    let tuple = (1, 2, 3);

    point.x;
    tuple.0;
}
```

## Reading from constants

![Minimum Rust version: 1.31](https://img.shields.io/badge/Minimum%20Rust%20Version-1.31-brightgreen.svg)

You can read from a constant:

```
const FOO: i32 = 5;

const fn foo() -> i32 {
    FOO
}
```

Note that this is *only* `const`, not `static`.

## & and * of references

![Minimum Rust version: 1.31](https://img.shields.io/badge/Minimum%20Rust%20Version-1.31-brightgreen.svg)

You can create and de-reference references:

```
const fn foo(r: &i32) {
    *r;

    &5;
}
```

## Casts, except for raw pointer to integer casts

![Minimum Rust version: 1.31](https://img.shields.io/badge/Minimum%20Rust%20Version-1.31-brightgreen.svg)

You may cast things, except for raw pointers may not be casted to an integer:

```
const fn foo() {
    let x: usize = 5;

    x as i32;
}
```

## Irrefutable destructuring patterns

![Minimum Rust version: 1.33](https://img.shields.io/badge/Minimum%20Rust%20Version-1.33-brightgreen.svg)

You can use irrefutable patterns that destructure values. For example:

```
const fn foo((x, y): (u8, u8)) {
    // ...
}
```

Here, `foo` destructures the tuple into `x` and `y`. `if let` is another
place that uses irrefutable patterns.

## `let` bindings

![Minimum Rust version: 1.33](https://img.shields.io/badge/Minimum%20Rust%20Version-1.33-brightgreen.svg)

You can use both mutable and immutable `let` bindings:

```
const fn foo() {
    let x = 5;
    let mut y = 10;
}
```

## Assignment

![Minimum Rust version: 1.33](https://img.shields.io/badge/Minimum%20Rust%20Version-1.33-brightgreen.svg)

You can use assignment and assignment operators:

```
const fn foo() {
    let mut x = 5;
    x = 10;
}
```

## Calling `unsafe fn`

![Minimum Rust version: 1.33](https://img.shields.io/badge/Minimum%20Rust%20Version-1.33-brightgreen.svg)

YOu can call an `unsafe fn` inside a `const fn`:

```
const unsafe fn foo() -> i32 { 5 }

const fn bar() -> i32 {
    unsafe { foo() }
}
```