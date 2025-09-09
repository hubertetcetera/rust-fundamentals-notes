# Match Control Flow Construct

Say, I want you to write a function that returns how much ice cream there is left in the fridge like so:

If it's before 22:00 (24-hour system), then 5 scoops are left.
At 22:00, someone eats it all, so no ice cream is left (value 0).
Return `None` if `hour_of_day` is higher than 23.

If you're not used to the range pattern in Rust, your first instinct (like me), may be to reach for conditional like:

```rust
if hour_of_day < 22 {
    Some(5)
} else if hour_of_day > 23 {
    None
} else {
    Some(0)
}
```

But this has a few problems:

1. No compiler exhaustiveness checking, so it is easier to miss subtle cases if logic grows.
2. Scales poorly as cases increase.

Rust's `match` pattern combined with `ranges` addresses both these issues easily:

```rust
match hour_of_day {
    0..22 => Some(5),
    22..=23 => Some(0),
    _ => None,
}
```

However, the reason for why this section exists in these notes is because of a curious mistake I made when solving this as a beginner. Here's how I implemented it:

```rust
match h {
    h if h < 22 => …,
    h if h > 23 => …,
    _ => …,
}
```

Hopefully, you can immediately tell that this is even worse that the plain `if`/`else` implementation. But for some reason, my brain had internalized "Rust-y" thinking as "use `match`" without thinking through exactly what this implies.

On the surface, it _felt_ more Rusty than using plain old `if`/`else`, even though idiomatically, it isn't. Let's unpack that:

A match is normally compiled into a [jump table](https://en.wikipedia.org/wiki/Branch_table) or range check. BUT once you add guards (`if` conditions), those arms effectively become if-statements inside the match machinery.

So the compiler lowers it to:

1. Test pattern matches (always true here, since h matches anything).
2. Check guard conditions in sequence.

**Net result:** same sequence of comparisons as `if`/`else`, but with a little more branching setup.

In this specific case, the performance hit is negligible. But the “Negligible” argument only holds for tiny cases like this 3-branch example. Guard usage like this can block better codegen in larger or hot paths (fancy way of saying, "executed often"). Not to mention how hard it makes it for anyone reading the code to understand what's going on.

### What can go wrong if we keep the guard-y version?

1. Weaker compiler help

- With guards, the compiler can’t reason about the numeric space as well (also true for `if`/`else`).
- It won’t warn you if your predicates accidentally leave gaps or overlap in surprising ways (it only sees “some condition” instead of a range).
- With ranges, the compiler knows coverage (e.g., 0..22, 22..=23, \_) and can warn on unreachable or uncovered arms.

2. Order-dependence & refactor hazards

- Guards are checked in order. A future tweak like inserting h if h <= 22 above the first arm can silently change behavior.
- Ranges are declarative: 0..22 can’t accidentally “steal” values from another arm due to ordering.

3. Off-by-one risk is higher

- Writing < 22 / > 23 / “else” invites off-by-one errors when you change boundaries.
- With ranges you make intent explicit (0..22, 22..=23).

4. Maintainability and Readability

- Guards force readers to simulate the logic in their head, making them harder to read.
- If tomorrow we were to add special cases (e.g., “quiet hours = 23..=5”), guard chains get messy fast.
- Ranges scale better as you can list the intervals directly and let the compiler help.

5. Missed optimization opportunities

- If this became a hot dispatch (lots of branches / called in a tight loop), guards can block jump-table/range optimizations the compiler might use with pure patterns.

## Patterns vs. Expressions

Another property of the `match` statement that caught me by surprise when I discovered it for the first time was that I couldn't match a string like this:

```rust,does_not_compile
let args[1]: Vec<String> = std::env::args().collect();

match args[1] {
  String::from("init") => println!("initialize"),
  _ => println!("exiting...")
}
```

If you try to compile the above code, you'll see the compiler complain about `fn` calls not being allowed in patterns.

```
error[E0164]: expected tuple struct or tuple variant, found associated function `String::from`
  --> src/main.rs:17:9
   |
17 |         String::from("init") => {
   |         ^^^^^^^^^^^^^^^^^^^^ `fn` calls are not allowed in patterns
   |
   = help: for more information, visit https://doc.rust-lang.org/book/ch19-00-patterns.html

For more information about this error, try `rustc --explain E0164`.
```

Yet, the following code works perfectly fine:

```rust
let args[1]: Vec<String> = std::env::args().collect();

if args[1] == String::from("init") {
    println!("initialize")
} else {
    println!("exiting...")
}
```

Similarly, if we construct a guard like `s if s == String::from("init")` within the `match` statement, then we can make our first example compile as well. We can also use the `.as_str()` method to parse the `args[1]` value and use string literals within the the match arms like this:

```rust
match args[1].as_str() {
    "init" => println!("initialize"),
    _ => println!("exiting..."),
}
```

So, what's going on here?

This is because `String::from("init")` is a _value_, not a _pattern_.

> **IMPORTANT**
>
> Patterns can be literals (e.g. "init", 42), destructuring (e.g. Some(x)), identifiers, wildcards, etc.
> But **you can’t call functions in a pattern**. `String::from("init")` is a function call expression, not a literal.

In a `match` statement, the left-hand side is parsed as a _pattern_.
But because patterns can’t contain arbitrary function calls or expressions our code fails to compile. This also explains why string literals work fine.

String literals like `"init"` are `&'static str` and hence known at compile time. But `String` is an owned, heap-allocated type. `String::from("init")` allocates memory at runtime.

You can’t put a runtime allocation in a pattern because the compiler must be able to _statically check_ that the pattern is something it can compare against without evaluating the code.

On the other hand, `==` is **a runtime expression**, so such restrictions do not apply when we use an if statement like `if args[1] == String::from("init")`.
