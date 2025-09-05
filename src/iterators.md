# Iterators

One important fact about iterators that I remind myself from time to time is that iterators _consumes_ the values in it as `next()` method is called. For details, see the example below from the Rust Book:


> ```rust
>     #[test]
>     fn iterator_demonstration() {
>         let v1 = vec![1, 2, 3];
> 
>         let mut v1_iter = v1.iter();
> 
>         assert_eq!(v1_iter.next(), Some(&1));
>         assert_eq!(v1_iter.next(), Some(&2));
>         assert_eq!(v1_iter.next(), Some(&3));
>         assert_eq!(v1_iter.next(), None);
>     }
> ```
> 
> Here, we needed to make `v1_iter` mutable because calling the next method on an iterator changes internal state that the iterator uses to keep track of where it is in the sequence. In other words, this code consumes, or uses up, the iterator. Each call to next eats up an item from the iterator. We didn’t need to make `v1_iter` mutable when we used a for loop because the loop took ownership of `v1_iter` and made it mutable behind the scenes.
> 
> Also note that the values we get from the calls to next are immutable references to the values in the vector. The iter method produces an iterator over immutable references. If we want to create an iterator that takes ownership of `v1` and returns owned values, we can call `into_iter` instead of iter. Similarly, if we want to iterate over mutable references, we can call `iter_mut` instead of `iter`.

Iterator has various methods called _consuming adapters_ (like `sum`) which call `next` in their definition and hence _consume_ the iterator as they are called.