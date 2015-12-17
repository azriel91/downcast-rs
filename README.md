# Downcast

Rust enums are great for types where all variations are known beforehand. But in
the case where you want to implement a container of user-defined types, an
open-ended type like a trait object is needed. In some cases, it is useful to
cast the trait object back into its original concrete type to access additional
functionality and performant inlined implementations.

`downcast` is an exercise in adding basic down-casting support to trait objects
just as [MOPA](https://crates.io/crates/mopa/) and
[downcast](https://crates.io/crates/downcast/) do while avoiding unsafe code and
without replicating the behavior in the standard library. This is at the
(negligible) expense of adding two methods to the down-castable trait's vtable.

To make a trait downcastable, make it extend the `downcast::Downcast` trait and
invoke `impl_downcast!` on it as follows:

```rust
trait Trait: Downcast {}
impl_downcast!(Trait);
```

# Example

```rust
#[macro_use]
extern crate downcast;
use downcast::Downcast;

// To create a trait with downcasting methods, extend `Downcast` and run
// impl_downcast!() on the trait.
trait Base: Downcast {}
impl_downcast!(Base);

// Concrete type implementing Base.
struct Foo(u32);
impl Base for Foo {}

fn main() {
    // Create a trait object.
    let mut base: Box<Base> = Box::new(Foo(42));

    // Downcast to Foo.
    assert_eq!(base.downcast_ref::<Foo>().unwrap().0, 42);
}
```

# License

Copyright 2015, Ashish Myles.
This software is dual-licensed under the [MIT](LICENSE-MIT) and
[Apache 2.0](LICENSE-APACHE) licenses.
