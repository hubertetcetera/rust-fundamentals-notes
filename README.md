# 🦀 rust-fundamentals-notes

A collection of my personal notes while learning Rust.
It’s not meant to be a comprehensive Rust guide — there are already excellent resources like [The Rust Book](https://doc.rust-lang.org/book/) and [Rust by Example](https://doc.rust-lang.org/rust-by-example/).

This is a **curated set of concepts that stood out to me as non-trivial** when I first encountered them.
Think of it as a field guide: the places where you might stumble, the patterns that finally trigger an “aha” moment and the bits of Rust I keep coming back to.

## ✨ What you’ll find here

- Ownership & borrowing patterns that aren’t obvious at first glance
- Gotchas around lifetimes, references, and smart pointers
- Practical notes on `Result`, error handling, and common crates
- Data structure experiments (`Rc`, `RefCell`, arenas, etc.)
- Miscellaneous “aha” moments that felt worth writing down

## 📚 Who is this for?

Primarily me — I use it for quick revision and reinforcement.
But if you’re new to Rust, you might find some of these notes useful, especially the parts that aren’t always obvious from reading the docs.

## 🚀 Usage

This repo is built with [mdBook](https://rust-lang.github.io/mdBook/).
You can browse the notes locally:

```bash
cargo install mdbook
mdbook serve --open
```

## 🔧 Tooling

This repo uses [dprint](https://dprint.dev/) to keep Markdown, JSON, TOML, and YAML files consistently formatted.

```bash
cargo install dprint
dprint fmt
```

Run `dprint check` before committing to ensure formatting passes CI.

## 🤝 Contributing

Contributions are welcome!

- Keep notes concise and focused (don’t try to rewrite the Rust Book).
- Add runnable examples in the `listings/` folder with proper `// ANCHOR` markers.
- Run `cargo test --workspace` to make sure examples compile.
- Format with `dprint fmt` before committing.

## 📝 Disclaimer

- This is not an official or exhaustive resource to learn rust and should not be used as a primary learning resource, these are just practical notes from my learning journey.
- If you want a complete reference, start with The Rust Book or the Rust Reference.
