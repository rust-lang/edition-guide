# Rustfmt: Assignment operator RHS indentation

## Summary

In the 2024 Edition, `rustfmt` now indents the right-hand side of an assignment operator relative to the last line of the left-hand side, providing a clearer delineation and making it easier to notice the assignment operator.

## Details

In Rust 2021 and before, if an assignment operator has a multi-line left-hand side, the indentation of the right-hand side will visually run together with the left-hand side:

```rust,ignore
impl SomeType {
    fn method(&mut self) {
        self.array[array_index as usize]
            .as_mut()
            .expect("thing must exist")
            .extra_info =
            long_long_long_long_long_long_long_long_long_long_long_long_long_long_long;

        self.array[array_index as usize]
            .as_mut()
            .expect("thing must exist")
            .extra_info = Some(ExtraInfo {
            parent,
            count: count as u16,
            children: children.into_boxed_slice(),
        });
    }
}
```

In the 2024 Edition, `rustfmt` now indents the right-hand side relative to the last line of the left-hand side:

```rust,ignore
impl SomeType {
    fn method(&mut self) {
        self.array[array_index as usize]
            .as_mut()
            .expect("thing must exist")
            .extra_info =
                long_long_long_long_long_long_long_long_long_long_long_long_long_long_long;

        self.array[array_index as usize]
            .as_mut()
            .expect("thing must exist")
            .extra_info = Some(ExtraInfo {
                parent,
                count: count as u16,
                children: children.into_boxed_slice(),
            });
    }
}
```

## Migration

The change can be applied automatically by running `cargo fmt` or `rustfmt` with the 2024 Edition. See the [Style edition] chapter for more information on migrating and how style editions work.

[Style edition]: rustfmt-style-edition.md
