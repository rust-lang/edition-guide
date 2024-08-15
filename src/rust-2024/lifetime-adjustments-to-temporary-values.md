# Lifetime adjustments to temporary values

🚧 The 2024 Edition has not yet been released and hence this section is still "under construction".
More information may be found in the tracking issue at <https://github.com/rust-lang/rust/issues/124085> and <https://github.com/rust-lang/rust/issues/123739>.

## Summary

This Edition observes two breaking changes to the handling of lifetimes assigned to temporary values, in the following two cases.

- In `if let $pat = $expr { .. } else { .. }` expression, the temporary values generated from evaluating `$expr` will be dropped before the program enters the `else` branch.
- Temporary values generated in evaluation of the [tail expression] of a [function] or closure body, or a [block] are instead dropped before local variables are dropped with Edition 2024.

[function]: ../../reference/items/functions.html
[block]: ../../reference/expressions/block-expr.html
[tail expression]: ../../reference/items/expressions.html

## Details

Edition 2024 brings about improvements in handling lifetime of temporary values, to reduce expected retention of such values and surprising runtime behaviour thereof. First it involves an `if let` expression. Up until Edition 2021, `if let` as an expression assigns a lifetime that can extend beyond the `if let` expression itself.

```rust,edition2021
// Up until Edition 2021
call(
    another_call(if let Enum::Pattern { value } = &self.get_value().method() {
        Some(value)
    } else {
        None
    })
);
```

In this example, the temporary value generated from evaluating the sub-expression `self.get_value()` will only be dropped at the end of the semicolon, even though at that program point its last use as a function argument for `another_call` is exhausted. Edition 2024 makes a correction to this lifetime assignment by shortening it up until the point where the then-block is completely evaluated or the program control enters the `else` block.

```rust,edition2024
// From Edition 2024
call(
    another_call(if let Enum::Pattern { value } = &self.get_value().method() {
        Some(value)
    }
    // At this point, `self.get_value()` is dropped
    else {
        None
    })
);
```

The other lifetime assignment rule changed by Edition 2024 involves tail expressions. It often comes as a surprise that, up until Edition 2021, temporary values in tail expressions are dropped later than the local variable bindings, as in the following example.

```rust,edition2021
// Up until Edition 2021
fn main() {
    let m = std::sync::Mutex::new(());
    *m.lock().unwrap() // error[E0597]: `m` does not live long enough
}
```

This yields the following error with Edition 2021.

```
error[E0597]: `m` does not live long enough
 --> src/main.rs:3:6
  |
2 |     let m = std::sync::Mutex::new(());
  |         - binding `m` declared here
3 |     *m.lock().unwrap()
  |      ^----------------
  |      |
  |      borrowed value does not live long enough
  |      a temporary with access to the borrow is created here ...
4 |     // 
5 | }
  | -
  | |
  | `m` dropped here while still borrowed
  | ... and the borrow might be used here, when that temporary is dropped and runs the `Drop` code for type `MutexGuard`
  |
help: consider adding semicolon after the expression so its temporaries are dropped sooner, before the local variables declared by the block are dropped
  |
3 |     *m.lock().unwrap();
  |                       +
```

Edition 2024 intends to make correction, so that the temporary value `m.lock().unwrap` is dropped first, followed by dropping the local variable `m`.

## Migration

It is always safe to rewrite `if let` with a `match`. Edition 2024 comes with an non-enforcing lint `if_let_rescope` which suggests a fix when a lifetime issue arises due to this change, or the lint detects that a temporary value with a custom, non-trivial `Drop` destructor is generated from the right-hand side of the `if let`. For instance, the earlier example involving `call` and `another_call` may be rewritten into the following, when the suggestion from `cargo fix` is accepted.

```rust,edition2024
call(
    another_call(match &self.get_value().method() {
        Enum::Pattern { value } => {
            Some(value)
        }
        _ => {
            None
        }
    })
);
// `self.get_value()` is dropped at this point, which exactly matches Edition 2021 semantics
```

Edition 2024 cannot deduce with complete confidence that the program semantics are preserved when the lifetime of such temporary values are shortened. For this reason, this lint remains non-binding but users are encouraged to set its level to `warn` in order to allow manual auditing.

Unfortunately, there is no semantics-preserving rewrite to shortened lifetime for temporary values in tail expressions, with the implementation of [^RFC3606]. A lint `tail_expr_drop_order` is introduced to detect if a temporary value with a custom, non-trivial `Drop` destructor is generated in a tail expression. Users are again encouraged to set its level to `warn` so that the temporary values could be audited with respect to the change.

[^RFC3606]: Details are documented at [RFC 3606](https://github.com/rust-lang/rfcs/pull/3606)
