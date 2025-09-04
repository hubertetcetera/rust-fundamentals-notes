# Ownership

Ownership is a unique feature in Rust that allows it to offer memory safety guarantees without a garbage collector. There's 3 key ownership rules:

1. Every value in Rust must have an owner.
2. There can be only one owner at a time. (although [`Rc\<T\>` smart pointer](./smart_pointers) gives us a way to define multiple owners)
3. Value is dropped when owner goes out of scope.

Example from the rust book:

<Listing number="1-1" caption="A variable and the scope in which it is valid">

```rust
    {                      // s is not valid here, since it's not yet declared
        let s = "hello";   // s is valid from this point forward

        // do stuff with s
    }                      // this scope is now over, and s is no longer valid
```

</Listing>

This allows Rust to automatically allocate and deallocate memory for most usecases without incurring performance hits while providing memory safety guarantees.

Data types like `i32`, `bool`, `char`, etc. have a fixed size known at compile time and hence can live on the stack and implement the `Copy` trait. So you can do something like:

```rust
    let x = 5;
    let y = x;
```

Where the value of `x` i.e. `5` gets copied on the stack and assigned to `y`, keeping both `x` and `y` usable.

Because `String`, `Vec`, etc. may have an unknown size at compile time, they _point_ to data that lives on the heap. And since copying data from the heap is an expensive operation, we need to explicity `.clone()` the values when needed. Therefore, if we try to do something similar with a `String` like so:

```rust
    let s1 = String::from("hello");
    let s2 = s1;
```

It won't have the same behavior, because the ownership of the `String` value is _moved_ to `s2`.

What happens behind the scene is Rust copies the _pointer_ data `s1` to `s2` (_shallow copy_) and invalidates `s1`. Effectively ownership of the `String` is moved to `s2` and making any further attempts to borrow or use `s1` invalid.

Rust does this because of rule 2, _"There can be only one owner at a time"_. This is because Rust uses ownership to determine when to drop a value and free the allocated memory. If it had multiple owners for the same value, for example both `s1` and `s2` pointing to the same data, it would try to free the memory twice at the end of the program, causing the _double free_ error which can lead to memory corruption, causing potential security vulnerabilities.

Conversely, if we assign a completely new value to an existing variable, Rust will `drop` and free the original value's memory immediately. Example:

```rust
    let mut s = String::from("hello");
    s = String::from("ahoy"); // nothing is referring to the original value on the heap and the value gets dropped

    println!("{s}, world!");
```