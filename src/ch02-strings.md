# Strings

Strings in Rust can have a few "gotcha"s sometimes. Most of these stem from the difference between `String` and string slices `&str`.

From the syntax, you might be able to tell that `String` is an owned data type whereas `&str` is a borrowed reference. So, the reason why we don't see use of `str` is because it is a _dynamically sized type_ (DST) and its size is not known at compile time and hence we use a `&` (or `Box<str>`, `Rc<str>`, etc.) to reference the value.

Since both `String` and `&str` reference similar data (allocated on the heap), some convenient methods can sometimes return unexpected types. This isn't really a problem, because the compiler checks and screams at you when you make a mistake, nevertheless it is good to know the following common methods and why they return one type over the other:

1. `trim`: This trims the value in place and hence returns a string slice `&str` without the need to own the data.
2. `replace`: Creates a new `String`, and copies the data from given string slice into it. While doing so, it attempts to find matches of a pattern. If it finds any, it replaces them with the replacement string slice.
3. `to_lowercase`: Since some unicode characters can expand into multiple characters when changing the case, this function returns a `String` instead of modifying the parameter in-place.

Notice that 2, 3 one thing in common: they both can lead to changes in the length of the string, meaning the old buffer may not have enough capacity, or may have “holes” after shrinking.

Rust guarantees memory safety and valid UTF-8, so it can’t just patch bytes in place unless the replacement has _exactly_ the same byte length as the match.

So the safe general solution is: **allocate a new buffer and copy data** to return a new `String`.

By contrast `trim` can (and does) simply return the start/end locations of the existing buffer.
