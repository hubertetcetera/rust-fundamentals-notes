# Modulo Operator

<div class="warning">
    WARNING

    This document is currently a work in progress.

</div>

Why create a section about something as trivial as a modulo operator (`%`)? Afterall it's mere job is to return a remainder.

That's exactly what I used to think until I ran into the following problem:

Say, we want to return the remainder when we divide `-1` by `24`. What do you think should be the answer?

If you said `-1`, you wouldn't be wrong, but if you said `23`, you'd still be right. Wait, what? How is it possible that we can have 2 correct answers to this question?

To understand this, we need to unpack how a division algorithm works.

> **The Division algorithm**
>
> Given any two integers \\(a\\) and \\(b\\), we can always find an integer \\(q\\) such that \\[a = q \cdot b + r\\] where \\(r\\) is an integer satisfying \\(0 \leq r \lt |\ b\ | \\) OR \\(|\ r\ | \lt |\ b\ | \\).

`rem_euclid(self, rhs: i32)`
