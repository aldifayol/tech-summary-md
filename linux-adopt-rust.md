# How Rust Interacts with C Inside the Linux Kernel

*A deep-dive into the architecture, safety model, and interoperability
that make Rust a growing part of the Linux kernel.*

## Introduction

Rust has officially moved beyond "experimental" status in the Linux
kernel, marking a significant milestone in systems programming. Rather
than replacing C, Rust is being introduced **alongside** it to improve
safety, reduce memory-related vulnerabilities, and help develop new
kernel components---especially drivers.

To make this coexistence possible, the kernel employs a carefully
layered integration model. This article explains:

-   What parts of the kernel currently use Rust\
-   Why Rust improves safety\
-   How Rust and C interact internally\
-   How memory, concurrency, and data structures are handled\
-   How Rust code is compiled and loaded inside the kernel

------------------------------------------------------------------------

# 1. Where Rust Is Used in the Linux Kernel Today

## 1.1 Drivers and Modules

Rust is currently used for:

-   New device drivers\
-   Experimental network PHY drivers\
-   Logging subsystems\
-   Dedicated test modules

## 1.2 Kernel Infrastructure Support

Linux includes:

-   Rust toolchain support\
-   Kernel-provided Rust crates\
-   Safe wrappers around C APIs\
-   Build system integration (Kbuild)

## 1.3 Not Rewriting the Kernel

Critical C subsystems such as:

-   Scheduler\
-   Memory management\
-   Filesystem internals

remain in C.

------------------------------------------------------------------------

# 2. Why Rust Improves Safety Compared to C

## 2.1 Ownership & Borrow Checker

Rust enforces strict rules that prevent:

-   use-after-free\
-   double-free\
-   dangling pointers\
-   out-of-bounds access

## 2.2 No Null or Dangling Pointers

Rust's `Option<T>` avoids null pointer issues.

## 2.3 Memory Safety Without Garbage Collection

Rust offers C-level performance without GC pauses.

## 2.4 Eliminates Entire Bug Classes

Rust prevents:

-   Buffer overflows\
-   Uninitialized memory\
-   Data races (safe code)

## 2.5 Safer Concurrency

Rust uses `Send`, `Sync`, and strict borrowing rules.

------------------------------------------------------------------------

# 3. How Rust and C Interact in the Kernel

Rust communicates with C through a layered model:

    Rust Code (safe)
            ↓
    Rust Wrappers (safe)
            ↓
    Rust `unsafe` FFI bindings (unsafe)
            ↓
    C Kernel APIs (unsafe)

------------------------------------------------------------------------

## 3.1 Kernel Still Provides C APIs

Example:

``` c
void *kmalloc(size_t size, gfp_t flags);
```

------------------------------------------------------------------------

## 3.2 Rust Wraps These APIs in Safe Abstractions

``` rust
pub fn try_alloc<T>() -> Result<Box<T>> {
    // internally calls kmalloc
}
```

------------------------------------------------------------------------

## 3.3 Rust Code Uses These Wrappers

``` rust
let buffer = Box::try_new(0u8)?;
```

------------------------------------------------------------------------

## 3.4 `unsafe` Blocks Are Isolated

``` rust
unsafe {
    bindings::some_kernel_fn(ptr);
}
```

------------------------------------------------------------------------

# 4. How Function Calls Work (FFI Layer)

``` rust
extern "C" {
    fn kmalloc(size: usize, flags: u32) -> *mut c_void;
}
```

------------------------------------------------------------------------

# 5. Data Structures Shared Between Rust and C

## 5.1 C Struct Example

``` c
struct device_driver {
    int (*probe)(...);
    void (*remove)(...);
};
```

## 5.2 Rust Callback Implementation

``` rust
extern "C" fn probe(...) -> i32 {
    // Rust logic here
}
```

------------------------------------------------------------------------

# 6. How Rust Models Kernel Memory & Concurrency

## 6.1 Memory Model

Uses kernel-compatible versions of:

-   `Box<T>`
-   `Arc<T>`
-   `Vec<T>`

## 6.2 Pinning

Rust ensures stable memory addresses via `Pin<T>`.

## 6.3 Concurrency

Kernel lock primitives are exposed safely:

``` rust
let mut guard = spinlock.lock();
*guard += 1;
```

------------------------------------------------------------------------

# 7. How Rust Drivers Are Compiled and Loaded

## 7.1 Compilation Pipeline

-   `rustc` in `no_std` mode\
-   Kernel custom target

## 7.2 Kbuild Integration

``` makefile
obj-$(CONFIG_MY_RUST_DRIVER) += my_driver.o
```

## 7.3 Module Loading

``` bash
sudo insmod my_driver.ko
```

------------------------------------------------------------------------

# Conclusion

Rust's integration into the Linux kernel brings strong memory safety
guarantees without sacrificing performance. While C remains dominant,
Rust is increasingly used to implement safer drivers and new subsystems.
Together, Rust and C form a hybrid model that enhances reliability and
security across the kernel.

## Source
[!Linux Adopt Rust](https://thenewstack.io/rust-goes-mainstream-in-the-linux-kernel/)
