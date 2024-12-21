# Rustfmt: Formatting fixes

## Summary

- Fixes to various formatting scenarios.

## Details

The 2024 style edition introduces several fixes to various formatting scenarios.

### Don't align unrelated trailing comments after items or at the end of blocks

<!--
https://github.com/rust-lang/rustfmt/pull/3833
-->

Previously rustfmt would assume that a comment on a line following an item with a trailing comment should be indented to match the trailing comment. This has been changed so that those comments are not indented.

**Style edition 2021:**

```rust,ignore
pub const IFF_MULTICAST: ::c_int = 0x0000000800; // Supports multicast
                                                 // Multicast using broadcst. add.

pub const SQ_CRETAB: u16 = 0x000e; // CREATE TABLE
pub const SQ_DRPTAB: u16 = 0x000f; // DROP TABLE
pub const SQ_CREIDX: u16 = 0x0010; // CREATE INDEX
                                   //const SQ_DRPIDX: u16 = 0x0011; // DROP INDEX
                                   //const SQ_GRANT: u16 = 0x0012;  // GRANT
                                   //const SQ_REVOKE: u16 = 0x0013; // REVOKE

fn foo() {
    let f = bar(); // Donec consequat mi. Quisque vitae dolor. Integer lobortis. Maecenas id nulla. Lorem.
                   // Id turpis. Nam posuere lectus vitae nibh. Etiam tortor orci, sagittis
                   // malesuada, rhoncus quis, hendrerit eget, libero. Quisque commodo nulla at
    let b = baz();

    let normalized = self.ctfont.all_traits().normalized_weight(); // [-1.0, 1.0]
                                                                   // TODO(emilio): It may make sense to make this range [.01, 10.0], to align
                                                                   // with css-fonts-4's range of [1, 1000].
}
```

**Style edition 2024:**

```rust,ignore
pub const IFF_MULTICAST: ::c_int = 0x0000000800; // Supports multicast
// Multicast using broadcst. add.

pub const SQ_CRETAB: u16 = 0x000e; // CREATE TABLE
pub const SQ_DRPTAB: u16 = 0x000f; // DROP TABLE
pub const SQ_CREIDX: u16 = 0x0010; // CREATE INDEX
//const SQ_DRPIDX: u16 = 0x0011; // DROP INDEX
//const SQ_GRANT: u16 = 0x0012;  // GRANT
//const SQ_REVOKE: u16 = 0x0013; // REVOKE

fn foo() {
    let f = bar(); // Donec consequat mi. Quisque vitae dolor. Integer lobortis. Maecenas id nulla. Lorem.
    // Id turpis. Nam posuere lectus vitae nibh. Etiam tortor orci, sagittis
    // malesuada, rhoncus quis, hendrerit eget, libero. Quisque commodo nulla at
    let b = baz();

    let normalized = self.ctfont.all_traits().normalized_weight(); // [-1.0, 1.0]
    // TODO(emilio): It may make sense to make this range [.01, 10.0], to align
    // with css-fonts-4's range of [1, 1000].
}
```

### Don't indent strings in comments

<!--
https://github.com/rust-lang/rustfmt/pull/3284

https://github.com/rust-lang/rustfmt/pull/3326 -- Fixes this in macros.

NOTE: This also claims to change other things (such as idempotency), but that seems like it was independently fixed in previous versions.
-->

Previously rustfmt would incorrectly attempt to format strings in comments.

**Original:**

```rust,ignore
pub fn main() {
    /*   let s = String::from(
        "
hello
world
",
    ); */
}
```

**Style edition 2021:**

```rust,ignore
pub fn main() {
    /*   let s = String::from(
            "
    hello
    world
    ",
        ); */
}
```

**Style edition 2024:**

No change from original.

### Long strings don't prevent formatting expressions

<!--
https://github.com/rust-lang/rustfmt/issues/5577#issuecomment-1331628360
https://github.com/rust-lang/rustfmt/issues/4800
-->

In some situations, long strings would previously prevent the expression from being formatted.

**Style edition 2021:**

```rust,ignore
fn main() {
    let value = if x == "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum." { 0 } else {10};

    let x = Testing {
              foo: "long_long_long_long_long_long_long_lo_long_long_long_long_long_long__long_long_long_long_long_long_",
bar: "long_long_long_long_long_long_long_long_long_long_lo_long_long_lolong_long_long_lo_long_long_lolong_long_long_lo_long_long_lo",
};
}
```

**Style edition 2024:**

```rust,ignore
fn main() {
    let value = if x
        == "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
    {
        0
    } else {
        10
    };

    let x = Testing {
        foo: "long_long_long_long_long_long_long_lo_long_long_long_long_long_long__long_long_long_long_long_long_",
        bar: "long_long_long_long_long_long_long_long_long_long_lo_long_long_lolong_long_long_lo_long_long_lolong_long_long_lo_long_long_lo",
    };
}
```

### Fixed indentation of generics in impl blocks

<!--
https://github.com/rust-lang/rustfmt/pull/3856
-->

Generics in `impl` items had excessive indentation.

**Style edition 2021:**

```rust,ignore
impl<
        Target: FromEvent<A> + FromEvent<B>,
        A: Widget2<Ctx = C>,
        B: Widget2<Ctx = C>,
        C: for<'a> CtxFamily<'a>,
    > Widget2 for WidgetEventLifter<Target, A, B>
{
    type Ctx = C;
    type Event = Vec<Target>;
}
```

**Style edition 2024:**

```rust,ignore
impl<
    Target: FromEvent<A> + FromEvent<B>,
    A: Widget2<Ctx = C>,
    B: Widget2<Ctx = C>,
    C: for<'a> CtxFamily<'a>,
> Widget2 for WidgetEventLifter<Target, A, B>
{
    type Ctx = C;
    type Event = Vec<Target>;
}
```

### Use correct indentation when formatting a complex `fn`

<!--
https://github.com/rust-lang/rustfmt/pull/3731
-->

In some cases, a complex `fn` signature could end up with an unusual indentation that is now fixed.

**Style edition 2021:**

```rust,ignore
fn build_sorted_static_get_entry_names(
    mut entries: Vec<(u8, &'static str)>,
) -> (impl Fn(
    AlphabeticalTraversal,
    Box<dyn dirents_sink::Sink<AlphabeticalTraversal>>,
) -> BoxFuture<'static, Result<Box<dyn dirents_sink::Sealed>, Status>>
        + Send
        + Sync
        + 'static) {
}
```

**Style edition 2024:**

```rust,ignore
fn build_sorted_static_get_entry_names(
    mut entries: Vec<(u8, &'static str)>,
) -> (
    impl Fn(
        AlphabeticalTraversal,
        Box<dyn dirents_sink::Sink<AlphabeticalTraversal>>,
    ) -> BoxFuture<'static, Result<Box<dyn dirents_sink::Sealed>, Status>>
    + Send
    + Sync
    + 'static
) {
}
```

### Avoid extra space in nested tuple indexing expression

<!--
https://github.com/rust-lang/rustfmt/pull/4503
-->

Nested tuple indexing expressions would incorrectly include an extra space.

**Style edition 2021:**

```rust,ignore
fn main() {
    let _ = ((1,),).0 .0;
}
```

**Style edition 2024:**

```rust,ignore
fn main() {
    let _ = ((1,),).0.0;
}
```

### End return/break/continue inside a block in a match with a semicolon

<!--
https://github.com/rust-lang/rustfmt/pull/3223
https://github.com/rust-lang/rustfmt/pull/3250
-->

A `return`, `break`, or `continue` inside a block in a match arm was incorrectly missing a semicolon.

**Style edition 2021:**

```rust,ignore
fn foo() {
    match 0 {
        0 => {
            return AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
        }
        _ => "",
    };
}
```

**Style edition 2024:**

```rust,ignore
fn foo() {
    match 0 {
        0 => {
            return AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA;
        }
        _ => "",
    };
}
```

### Long array and slice patterns are now wrapped

<!--
https://github.com/rust-lang/rustfmt/pull/4994
-->

Long array and slice patterns were not getting wrapped properly.

**Style edition 2021:**

```rust,ignore
fn main() {
    let [aaaaaaaaaaaaaaaaaaaaaaaaaa, bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb, cccccccccccccccccccccccccc, ddddddddddddddddddddddddd] =
        panic!();
}
```

**Style edition 2024:**

```rust,ignore
fn main() {
    let [
        aaaaaaaaaaaaaaaaaaaaaaaaaa,
        bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb,
        cccccccccccccccccccccccccc,
        ddddddddddddddddddddddddd,
    ] = panic!();
}
```

### Format the last expression-statement as an expression

<!--
https://github.com/rust-lang/rustfmt/pull/3631
https://github.com/rust-lang/rustfmt/pull/3338
-->

The last statement in a block which is an expression is now formatted as an expression.

**Style edition 2021:**

```rust,ignore
fn main() {
    let toto = || {
        if true {
            42
        } else {
            24
        }
    };

    {
        T
    }
}
```

**Style edition 2024:**

```rust,ignore
fn main() {
    let toto = || {
        if true { 42 } else { 24 }
    };

    { T }
}
```

### Same formatting between function and macro calls

<!--
https://github.com/rust-lang/rustfmt/pull/3298
-->

Some formatting is now the same in a macro invocation as it is in a function call.

**Style edition 2021:**

```rust,ignore
fn main() {
    macro_call!(HAYSTACK
        .par_iter()
        .find_any(|&&x| x[0] % 1000 == 999)
        .is_some());

    fn_call(
        HAYSTACK
            .par_iter()
            .find_any(|&&x| x[0] % 1000 == 999)
            .is_some(),
    );
}
```

**Style edition 2024:**

```rust,ignore
fn main() {
    macro_call!(
        HAYSTACK
            .par_iter()
            .find_any(|&&x| x[0] % 1000 == 999)
            .is_some()
    );

    fn_call(
        HAYSTACK
            .par_iter()
            .find_any(|&&x| x[0] % 1000 == 999)
            .is_some(),
    );
}
```

### Force block closures for closures with a single loop body

<!--
https://github.com/rust-lang/rustfmt/pull/3334
-->

Closures with a single loop are now formatted as a block expression.

**Style edition 2021:**

```rust,ignore
fn main() {
    thread::spawn(|| loop {
        println!("iteration");
    });
}
```

**Style edition 2024:**

```rust,ignore
fn main() {
    thread::spawn(|| {
        loop {
            println!("iteration");
        }
    });
}
```

### Empty lines in where clauses are now removed

<!--
https://github.com/rust-lang/rustfmt/pull/5867
-->

Empty lines in a `where` clause are now removed.

**Style edition 2021:**

```rust,ignore
fn foo<T>(_: T)
where
    T: std::fmt::Debug,

    T: std::fmt::Display,
{
}
```

**Style edition 2024:**

```rust,ignore
fn foo<T>(_: T)
where
    T: std::fmt::Debug,
    T: std::fmt::Display,
{
}
```

### Fixed formatting of a let-else statement with an attribute

<!--
https://github.com/rust-lang/rustfmt/pull/5902
-->

If a let-else statement had an attribute, then it would cause the `else` clause to incorrectly wrap the `else` part separately.

**Style edition 2021:**

```rust,ignore
fn main() {
    #[cfg(target_os = "linux")]
    let x = 42
    else {
        todo!()
    };

    // This is the same without an attribute.
    let x = 42 else { todo!() };
}
```

**Style edition 2024:**

```rust,ignore
fn main() {
    #[cfg(target_os = "linux")]
    let x = 42 else { todo!() };

    // This is the same without an attribute.
    let x = 42 else { todo!() };
}
```

### Off-by-one error for wrapping enum variant doc comments

<!--
https://github.com/rust-lang/rustfmt/pull/6000
-->

When using the `wrap_comments` feature, the comments were being wrapped at a column width off-by-one.

**Original:**

```rust,ignore
pub enum Severity {
    /// But here, this comment is 120 columns wide and the formatter wants to split it up onto two separate lines still.
    Error,
    /// This comment is 119 columns wide and works perfectly. Lorem ipsum. lorem ipsum. lorem ipsum. lorem ipsum lorem.
    Warning,
}
```

**Style edition 2021:**

```rust,ignore
pub enum Severity {
    /// But here, this comment is 120 columns wide and the formatter wants to split it up onto two separate lines
    /// still.
    Error,
    /// This comment is 119 columns wide and works perfectly. Lorem ipsum. lorem ipsum. lorem ipsum. lorem ipsum lorem.
    Warning,
}
```

**Style edition 2024:**

```rust,ignore
pub enum Severity {
    /// But here, this comment is 120 columns wide and the formatter wants to split it up onto two separate lines still.
    Error,
    /// This comment is 119 columns wide and works perfectly. Lorem ipsum. lorem ipsum. lorem ipsum. lorem ipsum lorem.
    Warning,
}
```

### Off-by-one error for `format_macro_matchers`

<!--
https://github.com/rust-lang/rustfmt/pull/5582
-->

When using the `format_macro_matchers` feature, the matcher was being wrapped at a column width off-by-one.

**Style edition 2021:**

```rust,ignore
macro_rules! test {
    ($aasdfghj:expr, $qwertyuiop:expr, $zxcvbnmasdfghjkl:expr, $aeiouaeiouaeio:expr, $add:expr) => {{
        return;
    }};
}
```

**Style edition 2024:**

```rust,ignore
macro_rules! test {
    (
        $aasdfghj:expr, $qwertyuiop:expr, $zxcvbnmasdfghjkl:expr, $aeiouaeiouaeio:expr, $add:expr
    ) => {{
        return;
    }};
}
```

### Fixed failure with `=>` in comment after match `=>`

<!--
https://github.com/rust-lang/rustfmt/pull/6092
-->

In certain circumstances if a comment contained a `=>` after the `=>` in a match expression, this would cause a failure to format correctly.

**Style edition 2021:**

```rust,ignore
fn main() {
    match a {
        _ =>
        // comment with =>
                {
            println!("A")
        }
    }
}
```

**Style edition 2024:**

```rust,ignore
fn main() {
    match a {
        _ =>
        // comment with =>
        {
            println!("A")
        }
    }
}
```

### Multiple inner attributes in a match expression indented incorrectly

<!--
https://github.com/rust-lang/rustfmt/pull/6148
-->

Multiple inner attributes in a match expression were being indented incorrectly.

**Style edition 2021:**

```rust,ignore
pub fn main() {
    match a {
        #![attr1]
    #![attr2]
        _ => None,
    }
}
```

**Style edition 2024:**

```rust,ignore
pub fn main() {
    match a {
        #![attr1]
        #![attr2]
        _ => None,
    }
}
```

## Migration

The change can be applied automatically by running `cargo fmt` or `rustfmt` with the 2024 Edition. See the [Style edition] chapter for more information on migrating and how style editions work.

[Style edition]: rustfmt-style-edition.md
