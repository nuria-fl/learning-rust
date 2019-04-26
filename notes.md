- [Getting started](#getting-started)
- [Common Programming Concepts](#common-programming-concepts)
  - [Variables and Mutability](#variables-and-mutability)
  - [Data Types](#data-types)
  - [Functions](#functions)
  - [Control flow](#control-flow)

## Getting started

```
fn main() {
    println!("Hello, world!");
}
```

The `main` function is always the first code that runs in every executable Rust program. `!` means you are calling a _macro_ (what's a macro? no clue).

Compiling and running are different steps.

`rust main.rs` compiles and outputs binary executable. Run by `./main`.

`cargo run` builds and runs.

`cargo check` to validate without building executable, it's faster.

`cargo build --release` for optimization.

`cargo doc --open` generates docs of all crates you are using. Cool!

`String::new()` `::` means that it's an associated method of a type (a static method).

`&` indicates that an argument is a reference. Refs are immutables by default, `mut` can be used.

## Common Programming Concepts

### Variables and Mutability

#### Variables

By default variables are immutable.
You can make them mutable by adding `mut`, as in `let mut x = 5;`
Type can be inferred.

#### Constants

Constants are always immutable. They are declared using `const` instead of `let`, and you must always annotate the type.

`const MAX_POINTS: u32 = 100_000;` (underscores can be inserted in numeric literals to improve readability)

Can be declared in any scope.
Can only be set to a constant expression, not a result of a function.

#### Shadowing

With shadowing we can transform a value without having to make the variable mutable:

```
let x = 5;
let x = x + 1; // x is 6
let x = x * 2; // x is 12
```

### Data Types

#### Integers

A scalar type represents a single value. Rust has four primary scalar types: integers, floating-point numbers, Booleans, and characters.

Integer types can be signed or unsigned (possibility to be negative or positive), and need to specify the bits of space they take. Default is `i32`

```
Length	Signed	Unsigned
8-bit   i8      u8
16-bit  i16     u16
32-bi   i32     u32
64-bit  i64     u64
128-bit	i128    u128
arch    isize   usize
```

#### Floating-point

Floating-point are `f32` and `f64`. Default is `f64`.

#### Booleans

Boolean is `bool`

#### Character

Character `char` are specified with single quotes, as opposed to string literals.

#### Compound Types

Compound types (tuples and arrays) can group multiple values into one type.

##### Tuples

Tuples are a comma-separated list between parentheses. Once declared they cannot grow or shrink.

`let tup: (i32, f64, u8) = (500, 6.4, 1);`

To get the individual values out of a tuple, we can use pattern matching to destructure a tuple value, like: `let (x, y, z) = tup;`, Or access to an element directly with the index: `tup.0`

##### Arrays

Every element of an array must have the same type. They have a fixed length, like tuples.

A vector is a similar collection type provided by the standard library that is allowed to grow or shrink in size.

Array types are written like `let a: [i32; 5] = [1, 2, 3, 4, 5];`. Can be initialized similarly: `let a = [3; 5];`

Arrays can be sliced like `let nice_slice = &a[1..3]; // [2, 3, 4]`

### Functions

`fn` allows you to declare new functions. Function names are snake_case. Rust doesn’t care where you define your functions, only that they’re defined somewhere.

Type of function parameters _must_ be declared.

Functions contain Statements and Expressions. **Statements** are instructions that perform some action and do not return a value (like `let x = 5`). **Expressions** evaluate to a resulting value (like the block to create new scopes `{}`).

Expressions do not include ending semicolons. If you add a semicolon to the end of an expression, you turn it into a statement, which will then not return a value.

Function return types are declared with an arrow: `fn five() -> i32 { // ... }`

Statements don’t evaluate to a value, which is expressed by (), an empty tuple.

### Control Flow

```
if number < 5 {
  println!("condition was true");
} else {
  println!("condition was false");
}
```

Condition must be a value (no truthy/falsy stuff). All branches must return the same type

Rust has three kinds of loops: `loop`, `while`, and `for`

Break and return value of a loop with `break value`.

Iterate through arrays:

```
let a = [10, 20, 30, 40, 50];
for element in a.iter() {
    println!("the value is: {}", element);
}
```

Iterate through a range: `for number in (1..4) { // ... }`
