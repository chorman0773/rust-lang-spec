## Array Indexing [exprs.index]

Syntax:
```abnf
index-expr := <suffix-expr> "[" <expr> "]"

suffix-expr /= <index-expr>
```

1. Constraints: given the index-expr `base[idx]`, either `base`, shall be an expression of an array or slice type and `idx` shall be an expression of type `usize`, or `base` shall be of a type `T` and `sub` shall be of a type `U`, such that `T: core::ops::Index<U>`.

2. Panics: If `base` is *pre-mono known* to be of a array type `[T;N]`, and `idx`, after implicit coercions, is *pre-mono known* to be of type `usize`, panics if `idx` is greater than or equal to `N`. If `base` is *pre-mono known* to be of a slice type `[T]`, and `idx`, after implicit coercions, is *pre-mono known* to be of type `usize`, panics if `idx` is greater than or equal to the length of the slice.

3. Preconditions: If `base` is *pre-mono known* to be of of an array or slice type, and `idx`, after implicit coercions, is *pre-mono known* be of type `usize`, then the projection to the `idx` element of the array at the place denoted by `base` shall be inbounds of that allocation.

4. The index-expr `base[idx]` accesses element `idx` of the array `base`. `base` is a place expression, and `idx` is a value expression. Deref coercions are applied to `base` (`[coercion.deref]`). 

5. If `base` is *pre-mono known* to be of of an array or slice type, and `idx`, after implicit coercions, is *pre-mono known* be of type `usize`, then the result is a place that refers to the `idx` element of the array or slice `base`. 

[Note 1: Arrays are index from `0`, so the first element of the array is index `0`, the second index `1`, etc. The index `N` is out of bounds for an array of length `N`]

6. If `base` is not of an array or slice type, or `idx` is not of type `usize`, then the result of the expression is the same as either `*(<T as core::ops::Index::<U>>::index(&base, idx))` or `*(<T as core::ops::IndexMut<U>>::index_mut(&mut base,idx))` except that implicit coercions are not applied to `idx` unless the *implementor-set* for `T as core::ops::Index` contains only one element. 

[Note 2: The type `[T]` implements both the `core::ops::Index<U>` and `core::ops::IndexMut<U>` trait when `U` is a builtin-range type. The behaviour of these trait implementations are described in the library specification and are not documented here. Additionally the type `[T]` implements both the `core::ops::Index<usize>` and `core::ops::IndexMut<usize>` traits. The behaviour of these impls is to perform the operation described in (3). These impls are not used by an index-expr if `base` and `idx` are both *pre-mono known* to be an array or slice type and `usize` respectively.]

7. The resulting place is mutable if and only if `base` is mutable. The resulting place is not movable. If `base` is not of an array or slice type, the result is given by the call to `index_mut` if and only if the resulting place is (possibly as a subexpression of an index-expr, field-access-expr, or method-invocation-expr) assigned to or is mutably borrowed, otherwise the result is given by the call to `index`. 

8. If `base` is of an array or slice type, and `idx` is of type `usize`, then `base[idx]` is a *constant expression* if `base` and `idx` are both *constant expressions*. 