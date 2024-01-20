## Array Construction [exprs.array]

Syntax:
```abnf 
array-expr := "[" [<expr> [*("," <expr>)] [","] ] "]"

simple-expr /= <array-expr>
```

1. Constraints: Given the array-expr `[E0, E1, .., En]`, `n` shall be representible as a value of type `usize`, and each `Ei` for `0<=i<n` shall, after implicit coercions, be of the same type `T`, such that `T: core::marker::Sized`.

2. Mandates: Given the array-expr `[E0, E1, .., En]` where each `Ei` is of type `T`, `size_of::<T>()*n` shall be representible as a value of type `isize`. 

2. The array-expr `[E0, E1, .., En]` constructs the `n` element array of type `T`, such that the `i`th element of the resulting array is equivalent to `Ei` for `0<=i<n`. The type of the array-expr `[E0, E1, .., En]` is `[T;n]`

3. Given the array-expr `[E0, E1, .., En]`, each `Ei` for `0<=i<n` is a value expression, and the array-expr is a value expression.


5. If all of `E0, E1, .., En` are *constant expressions*, then `[E0, E1, .., En]` is a *constant expression*.


Syntax:
```abnf
repeat-expr := "[" <expr> ";" <expr> "]"

simple-expr /= <repeat-expr>
```

1. Constraints: Given the repeat-expr `[val ; N]`,  `N` shall be a *potentially-dependant* constant expression of `usize`, and `val` shall be of a type `T`, such that `T: core::marker::Sized`. If `N` is a *dependent constant expression* or `N > 2`, then either `T: core::marker::Copy` or `val` shall be a (potentially parenthesized) path-expr that denotes a const item. 

2. Mandates: Given the repeat-expr `[val ; N]`, where `val` is of type `T`, `size_of::<T>()*N` shall be representible as a value of type `isize`.

3. The repeat-expr `[val;N]` constructs the `N` element array of type `T`, such that the `i`th element of the resulting array is equivalent to `val`, for `0<=i<N`. The type of the resulting expression is `[T;N]`.

4. If `val` is a (potentially parenthesized) path-expr that denotes a const item, then it is evaluated `N` times to initialize each corresponding element of the array. Otherwise,  if `N` is equal to zero, then `val` is dropped. If `N` is equal to 1, then `val` is evaluated exactly once as the sole element of the resulting array. Otherwise, `val` is evaluated exactly once, and copied to each element of the array. 

5. Given the repeat-expr `[val;N]`, `val` is a value expression, and the resulting repeat-expr is a value expression.

[Note 1: The latter constraint means that the effectively largest `N` is `isize::MAX`, unless `T` is a zero-sized type. Types larger than 1 byte further constraint `n`]

6. If `val` is a *constant expression*, then `[val; N]` is a *constant expression*.

