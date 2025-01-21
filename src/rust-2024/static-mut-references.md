# Disallow references to static mut

## Summary

- The [`static_mut_refs`] lint level is now `deny` by default.
  This checks for taking a shared or mutable reference to a `static mut`.

[`static_mut_refs`]: ../../rustc/lints/listing/warn-by-default.html#static-mut-refs

## Details

The [`static_mut_refs`] lint detects taking a reference to a [`static mut`]. In the 2024 Edition, this lint is now `deny` by default to emphasize that you should avoid making these references.

<!-- edition2024 -->
```rust
static mut X: i32 = 23;
static mut Y: i32 = 24;

unsafe {
    let y = &X;             // ERROR: shared reference to mutable static
    let ref x = X;          // ERROR: shared reference to mutable static
    let (x, y) = (&X, &Y);  // ERROR: shared reference to mutable static
}
```

Merely taking such a reference in violation of Rust's mutability XOR aliasing requirement has always been *instantaneous* [undefined behavior], **even if the reference is never read from or written to**.  Furthermore, upholding mutability XOR aliasing for a `static mut` requires *reasoning about your code globally*, which can be particularly difficult in the face of reentrancy and/or multithreading.

Note that there are some cases where implicit references are automatically created without a visible `&` operator. For example, these situations will also trigger the lint:

<!-- edition2024 -->
```rust
static mut NUMS: &[u8; 3] = &[0, 1, 2];

unsafe {
    println!("{NUMS:?}");   // ERROR: shared reference to mutable static
    let n = NUMS.len();     // ERROR: shared reference to mutable static
}
```

## Alternatives

Wherever possible, it is **strongly recommended** to use instead an *immutable* `static` of a type that provides *interior mutability* behind some *locally-reasoned abstraction* (which greatly reduces the complexity of ensuring that Rust's mutability XOR aliasing requirement is upheld).

In situations where no locally-reasoned abstraction is possible and you are therefore compelled still to reason globally about accesses to your `static` variable, you must now use raw pointers such as can be obtained via the [`&raw const` or `&raw mut` operators][raw].  By first obtaining a raw pointer rather than directly taking a reference, (the safety requirements of) accesses through that pointer will be more familiar to `unsafe` developers and can be deferred until/limited to smaller regions of code.

[Undefined Behavior]: ../../reference/behavior-considered-undefined.html
[`static mut`]: ../../reference/items/static-items.html#mutable-statics
[`addr_of_mut!`]: https://docs.rust-lang.org/core/ptr/macro.addr_of_mut.html
[raw]: ../../reference/expressions/operator-expr.html#raw-borrow-operators

Note that the following examples are just illustrations and are not intended as full-fledged implementations. Do not copy these as-is. There are details for your specific situation that may require alterations to fit your needs. These are intended to help you see different ways to approach your problem.

It is recommended to read the documentation for the specific types in the standard library, the reference on [undefined behavior], the [Rustonomicon], and if you are having questions to reach out on one of the Rust forums such as the [Users Forum].

[undefined behavior]: ../../reference/behavior-considered-undefined.html
[Rustonomicon]: ../../nomicon/index.html
[Users Forum]: https://users.rust-lang.org/

### Don't use globals

This is probably something you already know, but if possible it is best to avoid mutable global state. Of course this can be a little more awkward or difficult at times, particularly if you need to pass a mutable reference around between many functions.

### Atomics

The [atomic types][atomics] provide integers, pointers, and booleans that can be used in a `static` (without `mut`).

```rust,edition2024
# use std::sync::atomic::Ordering;
# use std::sync::atomic::AtomicU64;

// Chnage from this:
//   static mut COUNTER: u64 = 0;
// to this:
static COUNTER: AtomicU64 = AtomicU64::new(0);

fn main() {
    // Be sure to analyze your use case to determine the correct Ordering to use.
    COUNTER.fetch_add(1, Ordering::Relaxed);
}
```

[atomics]: ../../std/sync/atomic/index.html

### Mutex or RwLock

When your type is more complex than an atomic, consider using a [`Mutex`] or [`RwLock`] to ensure proper access to the global value.

```rust,edition2024
# use std::sync::Mutex;
# use std::collections::VecDeque;

// Change from this:
//     static mut QUEUE: VecDeque<String> = VecDeque::new();
// to this:
static QUEUE: Mutex<VecDeque<String>> = Mutex::new(VecDeque::new());

fn main() {
    QUEUE.lock().unwrap().push_back(String::from("abc"));
    let first = QUEUE.lock().unwrap().pop_front();
}
```

[`Mutex`]: ../../std/sync/struct.Mutex.html
[`RwLock`]: ../../std/sync/struct.RwLock.html

### OnceLock or LazyLock

If you are using a `static mut` because you need to do some one-time initialization that can't be `const`, you can instead reach for [`OnceLock`] or [`LazyLock`] instead.

```rust,edition2024
# use std::sync::LazyLock;
#
# struct GlobalState;
#
# impl GlobalState {
#     fn new() -> GlobalState {
#         GlobalState
#     }
#     fn example(&self) {}
# }

// Instead of some temporary or uninitialized type like:
//     static mut STATE: Option<GlobalState> = None;
// use this instead:
static STATE: LazyLock<GlobalState> = LazyLock::new(|| {
    GlobalState::new()
});

fn main() {
    STATE.example();
}
```

[`OnceLock`] is similar to [`LazyLock`], but can be used if you need to pass information into the constructor, which can work well with single initialization points (like `main`), or if the inputs are available wherever you access the global.

```rust,edition2024
# use std::sync::OnceLock;
#
# struct GlobalState;
#
# impl GlobalState {
#     fn new(verbose: bool) -> GlobalState {
#         GlobalState
#     }
#     fn example(&self) {}
# }
#
# struct Args {
#     verbose: bool
# }
# fn parse_arguments() -> Args {
#     Args { verbose: true }
# }

static STATE: OnceLock<GlobalState> = OnceLock::new();

fn main() {
    let args = parse_arguments();
    let state = GlobalState::new(args.verbose);
    let _ = STATE.set(state);
    // ...
    STATE.get().unwrap().example();
}
```

[`OnceLock`]: ../../std/sync/struct.OnceLock.html
[`LazyLock`]: ../../std/sync/struct.LazyLock.html

### `no_std` one-time initialization

This example is similar to [`OnceLock`] in that it provides one-time initialization of a global, but it does not require `std` which is useful in a `no_std` context. Assuming your target supports atomics, then you can use an atomic to check for the initialization of the global. The pattern might look something like this:

```rust,edition2024
# use core::sync::atomic::AtomicUsize;
# use core::sync::atomic::Ordering;
#
# struct Args {
#     verbose: bool,
# }
# fn parse_arguments() -> Args {
#     Args { verbose: true }
# }
#
# struct GlobalState {
#     verbose: bool,
# }
#
# impl GlobalState {
#     const fn default() -> GlobalState {
#         GlobalState { verbose: false }
#     }
#     fn new(verbose: bool) -> GlobalState {
#         GlobalState { verbose }
#     }
#     fn example(&self) {}
# }

const UNINITIALIZED: usize = 0;
const INITIALIZING: usize = 1;
const INITIALIZED: usize = 2;

static STATE_INITIALIZED: AtomicUsize = AtomicUsize::new(UNINITIALIZED);
static mut STATE: GlobalState = GlobalState::default();

fn set_global_state(state: GlobalState) {
    if STATE_INITIALIZED
        .compare_exchange(
            UNINITIALIZED,
            INITIALIZING,
            Ordering::SeqCst,
            Ordering::SeqCst,
        )
        .is_ok()
    {
        // SAFETY: The reads and writes to STATE are guarded with the INITIALIZED guard.
        unsafe {
            STATE = state;
        }
        STATE_INITIALIZED.store(INITIALIZED, Ordering::SeqCst);
    } else {
        panic!("already initialized, or concurrent initialization");
    }
}

fn get_state() -> &'static GlobalState {
    if STATE_INITIALIZED.load(Ordering::Acquire) != INITIALIZED {
        panic!("not initialized");
    } else {
        // SAFETY: Mutable access is not possible after state has been initialized.
        unsafe { &*&raw const STATE }
    }
}

fn main() {
    let args = parse_arguments();
    let state = GlobalState::new(args.verbose);
    set_global_state(state);
    // ...
    let state = get_state();
    state.example();
}
```

This example assumes you can put some default value in the static before it is initialized (the const `default` constructor in this example). If that is not possible, consider using either [`MaybeUninit`], or dynamic trait dispatch (with a dummy type that implements a trait), or some other approach to have a default placeholder.

There are community-provided crates that can provide similar one-time initialization, such as the [`static-cell`] crate (which supports targets that do not have atomics by using [`portable-atomic`]).

[`MaybeUninit`]: ../../core/mem/union.MaybeUninit.html
[`static-cell`]: https://crates.io/crates/static_cell
[`portable-atomic`]: https://crates.io/crates/portable-atomic

### Raw pointers

In some cases you can continue to use `static mut`, but avoid creating references. For example, if you just need to pass [raw pointers] into a C library, don't create an intermediate reference. Instead you can use [raw borrow operators], like in the following example:

```rust,edition2024,no_run
# #[repr(C)]
# struct GlobalState {
#     value: i32
# }
#
# impl GlobalState {
#     const fn new() -> GlobalState {
#         GlobalState { value: 0 }
#     }
# }

static mut STATE: GlobalState = GlobalState::new();

unsafe extern "C" {
    fn example_ffi(state: *mut GlobalState);
}

fn main() {
    unsafe {
        // Change from this:
        //     example_ffi(&mut STATE as *mut GlobalState);
        // to this:
        example_ffi(&raw mut STATE);
    }
}
```

Just beware that you still need to uphold the aliasing constraints around mutable pointers. This may require some internal or external synchronization or proofs about how it is used across threads, interrupt handlers, and reentrancy.

[raw borrow operators]: ../../reference/expressions/operator-expr.html#raw-borrow-operators
[raw pointers]: ../../reference/types/pointer.html#raw-pointers-const-and-mut

### `UnsafeCell` with `Sync`

[`UnsafeCell`] does not impl `Sync`, so it cannot be used in a `static`. You can create your own wrapper around [`UnsafeCell`] to add a `Sync` impl so that it can be used in a `static` to implement interior mutability. This approach can be useful if you have external locks or other guarantees that uphold the safety invariants required for mutable pointers.

Note that this is largely the same as the [raw pointers](#raw-pointers) example. The wrapper helps to emphasize how you are using the type, and focus on which safety requirements you should be careful of. But otherwise they are roughly the same.

```rust,edition2024
# use std::cell::UnsafeCell;
#
# fn with_interrupts_disabled<T: Fn()>(f: T) {
#     // A real example would disable interrupts.
#     f();
# }
#
# #[repr(C)]
# struct GlobalState {
#     value: i32,
# }
#
# impl GlobalState {
#     const fn new() -> GlobalState {
#         GlobalState { value: 0 }
#     }
# }

#[repr(transparent)]
pub struct SyncUnsafeCell<T>(UnsafeCell<T>);

unsafe impl<T: Sync> Sync for SyncUnsafeCell<T> {}

static STATE: SyncUnsafeCell<GlobalState> = SyncUnsafeCell(UnsafeCell::new(GlobalState::new()));

fn set_value(value: i32) {
    with_interrupts_disabled(|| {
        let state = STATE.0.get();
        unsafe {
            // SAFETY: This value is only ever read in our interrupt handler,
            // and interrupts are disabled, and we only use this in one thread.
            (*state).value = value;
        }
    });
}
```

The standard library has a nightly-only (unstable) variant of [`UnsafeCell`] called [`SyncUnsafeCell`]. This example above shows a very simplified version of the standard library type, but would be used roughly the same way. It can provide even better isolation, so do check out its implementation for more details.

This example includes a fictional `with_interrupts_disabled` function which is the type of thing you might see in an embedded environment. For example, the [`critical-section`] crate provides a similar kind of functionality that could be used for an embedded environment.

[`critical-section`]: https://crates.io/crates/critical-section
[`UnsafeCell`]: ../../std/cell/struct.UnsafeCell.html
[`SyncUnsafeCell`]: ../../std/cell/struct.SyncUnsafeCell.html

### Safe references

In some cases it may be safe to create a reference of a `static mut`. The whole point of the [`static_mut_refs`] lint is that this is very hard to do correctly! However, that's not to say it is *impossible*. If you have a situation where you can guarantee that the aliasing requirements are upheld, such as guaranteeing the static is narrowly scoped (only used in a small module or function), has some internal or external synchronization, accounts for interrupt handlers and reentrancy, panic safety, drop handlers, etc., then taking a reference may be fine.

There are two approaches you can take for this. You can either allow the [`static_mut_refs`] lint (preferably as narrowly as you can), or convert raw pointers to a reference, as with `&mut *&raw mut MY_STATIC`.

<!-- TODO: Should we prefer one or the other here? -->

#### Short-lived references

If you must create a reference to a `static mut`, then it is recommended to minimize the scope of how long that reference exists. Avoid squirreling the reference away somewhere, or keeping it alive through a large section of code. Keeping it short-lived helps with auditing, and verifying that exclusive access is maintained for the duration. Using pointers should be your default unit, and only convert the pointer to a reference on demand when absolutely required.

## Migration

There is no automatic migration to fix these references to `static mut`. To avoid undefined behavior you must rewrite your code to use a different approach as recommended in the [Alternatives](#alternatives) section.
