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
