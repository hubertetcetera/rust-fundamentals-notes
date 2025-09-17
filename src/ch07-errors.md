# Errors in Rust

One of the many things that makes Rust great to work with is that the type system forces you to acknowledge the possibility of errors at compile time by using types like `Result<T, E>`.

I won't dive deep into how to handle errors in Rust, I think the Rust book does a great job explaining that. Broadly speaking, there's 4 different ways to handle errors:

1. **Panic** (with `panic!`, `unwrap` or `expect`)

- Not really "handling" the error at all.
- Crashes the program when an error occurs.
- Useful in quick prototypes or when failure is truly unrecoverable.

2. **Discard** (with `let _ = result;` or `.ok()` pattern)

- This isn't encouraged for most cases either (unless error is truly irrelevant).
- Explicitly ignores the error.
- This is rarely recommended, but sometimes fine if the error is truly irrelevant.

3. **Propagation** (using `?` operator)

- Returns the error to the caller a.k.a. "pushing error up the stack".
- This is the most common and idiomatic pattern in Rust.

4. **Transform** (using `map`, `map_err` `and_then`)

- Converts an error into another type or add context, so callers see a clearer error message.

## Defining Custom Error Types

<div class="warning">
    WARNING

This section is TBD.

</div>
