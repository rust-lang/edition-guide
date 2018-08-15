# Choosing alignment with the repr attribute

![Minimum Rust version: 1.25](https://img.shields.io/badge/Minimum%20Rust%20Version-1.25-brightgreen.svg)

From [Wikipedia](https://en.wikipedia.org/wiki/Data_structure_alignment):

> The CPU in modern computer hardware performs reads and writes to memory
> most efficiently when the data is naturally aligned, which generally means
> that the data address is a multiple of the data size. Data alignment refers
> to aligning elements according to their natural alignment. To ensure natural
> alignment, it may be necessary to insert some padding between structure
> elements or after the last element of a structure.

The `#[repr]` attribute has a new parameter, `align`, that sets the alignment of your struct:

```rust
struct Number(i32);

assert_eq!(std::mem::align_of::<Number>(), 4);
assert_eq!(std::mem::size_of::<Number>(), 4);

#[repr(align(16))]
struct Align16(i32);

assert_eq!(std::mem::align_of::<Align16>(), 16);
assert_eq!(std::mem::size_of::<Align16>(), 16);
```

If you’re working with low-level stuff, control of these kinds of things can
be very important!

The alignment of a type is normally not worried about as the compiler will
"do the right thing" of picking an appropriate alignment for general use
cases. There are situations, however, where a nonstandard alignment may be
desired when operating with foreign systems. For example these sorts of
situations tend to necessitate or be much easier with a custom alignment:

* Hardware can often have obscure requirements such as "this structure is
  aligned to 32 bytes" when it in fact is only composed of 4-byte values. While
  this can typically be manually calculated and managed, it's often also useful
  to express this as a property of a type to get the compiler to do a little
  extra work instead.
* C compilers like `gcc` and `clang` offer the ability to specify a custom
  alignment for structures, and Rust can much more easily interoperate with
  these types if Rust can also mirror the request for a custom alignment (e.g.
  passing a structure to C correctly is much easier).
* Custom alignment can often be used for various tricks here and there and is
  often convenient as "let's play around with an implementation" tool. For
  example this can be used to statically allocate page tables in a kernel or
  create an at-least cache-line-sized structure easily for concurrent
  programming.

The purpose of this feature is to provide a lightweight annotation to alter
the compiler-inferred alignment of a structure to enable these situations
much more easily.