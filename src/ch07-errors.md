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

TODOs:

- [ ] `Box<dyn std::error::Error>`
- [ ] `thiserror`
- [ ] `anyhow`
- [ ] How to think:
  - Low level functions: Precise definitions for exactly what the error is
  - Business Logic: Add context. What were you trying to do? Which object, path, or input failed?
  - Boundary level: What does the user see? e.g. `error: could not open file at ~/.app/config.toml (file not found)`

- [ ] `#[from]` vs `#[source]`
  - Use #[from]
    When the variant is just a passthrough of another error (no extra fields).
    You want auto-conversion so you can write clean `?` calls.
  - Use #[source]
    When your error variant has additional context fields (like a path, object ID, or operation).
    You still want to carry the original error, but you must build it manually so you can fill in the extra fields.
- [ ] Pitfalls
  - Redundant fields: if source already has path/status and you’ll display it, don’t duplicate unless you need it for matching or consistent UI.
  - Leaking secrets: never include tokens/keys in Display. If needed, store them, but print redacted (\*\*\*) or only in Debug logs.
  - Heavy fields: store identifiers, not whole objects (e.g., `&[u8]` lengths, not the data).
  - Brittle strings: prefer typed fields (`PathBuf`, `Url`, `StatusCode`) over embedding everything in the message.

**tl;dr:**
Start with target + cause (`path`, `#[source]`). Add only the one or two pieces of context that make the error actionable (`size`, `kind`, `offset`). Favor types over strings, and keep secrets out of `Display`.
