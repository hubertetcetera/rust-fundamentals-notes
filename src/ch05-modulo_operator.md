# Modulo Operator

Why create a section about something as trivial as a modulo operator (`%`)? Afterall its mere job is to return a remainder.

That's exactly what I used to think until I ran into the following problem:

Say, we want to return the remainder when we divide `-1` by `24`. What do you think should be the answer?

If you said `-1`, you wouldn't be wrong, but if you said `23`, you'd still be right. Wait, what? How is it possible that we can have 2 correct answers to this question?

To understand this, we need to unpack how a division algorithm works.

> **The Division algorithm**
>
> In math, dividing integers isn’t exact — so we define:
>
> Given any two integers \\(a\\) and \\(b\\), we can always find an integer \\(q\\) such that \\[a = q \cdot b + r\\] where \\(q\\) is the quotient and \\(r\\) is the remainder.

The trick is: **how do you pick q and r?**

We have different conventions that we could follow, where \\(r\\) is an integer satisfying either of the following:

1. \\(0 \leq r \lt |\ b\ | \\), _or_
2. \\(|\ r\ | \lt |\ b\ | \\)

Rust and most other programming languages use **Truncated division** for `%` operator, meaning \\(q\\) is chosen by chopping ("truncating") the real division \\(a\\div n\\) toward 0. Consequently, this can make the remainder negative.

Example:
\\(-1 = (0 \cdot 24) + (-1)\\)

This is consistent with “chop toward 0” but not very nice when you want wrap-around (for example, when you want to build a 24 hour clock).

Thankfully, Rust includes [`.rem_euclid`](https://doc.rust-lang.org/std/primitive.i32.html#method.rem_euclid) method on numeric data types which uses **Euclidean division**.

In Euclidean division, we require the remainder r to satisfy:

\\(0 \leq r < n\\)

That is, the remainder is always non-negative and less than the divisor.

Example:
\\(-1 = (-1 \cdot 24) + 23\\)

So `(-1).rem_euclid(24)` returns `23`.

**So the mathematical concept is:**

- `%` = truncated division remainder (can be negative).
- `rem_euclid` = Euclidean division remainder (always non-negative, canonical representative of the congruence class modulo n).

That’s why `rem_euclid` feels like a wrap-around: it maps any integer to its position on the circle of length `n` (perfect for a clock implementation!).
