# Documentation Basics

Rust makes it easy to generate standardized docs using `cargo doc`. It is as simple as using three slashes `///` and writing down the documentation for the specific snippet using markdown syntax. For example:

```rust
/// Adds one to the number given.
///
/// # Examples
///
/// ```
/// let arg = 5;
/// let answer = my_crate::add_one(arg);
///
/// assert_eq!(6, answer);
/// ```
pub fn add_one(x: i32) -> i32 {
    x + 1
}
```

To write useful and informative docs however, going through the following commonly used sections as a checklist (as mentioned in the Rust Book) is helpful:

> **Panics:** The scenarios in which the function being documented could panic. Callers of the function who don’t want their programs to panic should make sure they don’t call the function in these situations.
> 
> **Errors:** If the function returns a Result, describing the kinds of errors that might occur and what conditions might cause those errors to be returned can be helpful to callers so they can write code to handle the different kinds of errors in different ways.
> 
> **Safety:** If the function is unsafe to call (we discuss unsafety in Chapter 20), there should be a section explaining why the function is unsafe and covering the invariants that the function expects callers to uphold.
> 
> Most documentation comments don’t need all of these sections, but this is a good checklist to remind you of the aspects of your code users will be interested in knowing about.

Furthermore, it is a good idea to add tests to the documentation as running `cargo test` runs the code examples in the docs as tests. Meaning, if your code is out of sync with your docs, you'll be able to catch that while testing.
