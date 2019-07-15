- [Getting started](#getting-started)
- [Common Programming Concepts](#common-programming-concepts)
  - [Variables and Mutability](#variables-and-mutability)
  - [Data Types](#data-types)
  - [Functions](#functions)
  - [Control flow](#control-flow)
- [Understanding Ownership](#understanding-ownership)
  - [Stack and Heap](#stack-and-heap)
  - [Ownership](#ownership)
  - [References and Borrowing](#references-and-borrowing)
  - [The Slice Type](#the-slice-type)
- [Structs](#structs)
  - [Derived traits](#derived-traits)
  - [Methods](#methods)
  - [Associated functions](#associated-functions)
- [Enums and Pattern Matching](#enums-and-pattern-matching)
  - [Enums](#enums)
  - [`match`](#match)
  - [`if let`](#if-let)
- [Packages, Crates and Modules](packages-crates-and-modules)
  - [Packages and Crates](packages-and-crates)
  - [Modules](modules)
- [Concurrency](concurrency)
  - [Threads](threads)
  - [Transfer data between threads](transfer-data-between-threads)

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

## Understanding Ownership

### Stack and Heap

Stack and the heap are parts of memory that are available to your code to use at runtime.

Stack is LIFO. All data stored there must have a known, fixed size.

Heap is less structured. When you put data there, the system finds an empty spot somewhere in the heap that is big enough, marks it as being in use, and returns a pointer, which is the address of that location. This is _allocating_.

Pushing to the stack is faster than allocating on the heap, likewise, accessing data in the heap is slower.

When your code calls a function, the values passed into the function (including, potentially, pointers to data on the heap) and the function’s local variables get pushed onto the stack. When the function is over, those values get popped off the stack.

### Ownership

Each value in Rust has a variable that’s called its _owner_.

There can only be one owner at a time.

When the owner goes out of scope, the value will be dropped.

`String` is different from a string literal. It can be mutated and is allocated on the heap. Rust automatically returns memory once the variable that owns it goes out of scope (Rust calls a `drop` method at that point).

```
let s1 = String::from("hello");
let s2 = s1;

println!("{}, world!", s1); // error!
```

When we do this, only `String` pointer, length and capacity are passed to `s2`, this is on the stack. We do not copy the data on the heap. To avoid _double free_ error (trying to free memory twice), Rust invalidates `s1` when it's _copied_ to `s2`. So it's not actually copied, it's **moved**. Rust will never automatically create “deep” copies.

Deep copy of the heap data can be done using `clone` method.

```
let x = 5;
let y = x;

println!("x = {}, y = {}", x, y); // x is still valid
```

Types that have a known size at compile time are stored on the stack and can be copied inexpensively, so `x` is not moved into `y` and is still valid.

Rust has a special annotation called the Copy trait that we can place on types like integers that are stored on the stack. If a type has the Copy trait, an older variable is still usable after assignment. Rust won’t let us annotate a type with the Copy trait if the type, or any of its parts, has implemented the Drop trait. _(I don't understand shit about this)_

Passing a variable to a function will move or copy, just as assignment does.

```
let s = String::from("hello");  // s comes into scope

takes_ownership(s);             // s's value moves into the function...
                                // ... and so is no longer valid here

let x = 5;                      // x comes into scope

makes_copy(x);                  // x would move into the function, but i32 is Copy, so it’s okay to still use x afterward
```

### References and Borrowing

If we want to let a function use a value but not take ownership, we can pass a _reference_ using `&`:

```
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}
```

Having references as function parameters is called _borrowing_. We cannot modify a borrowed value because by default is immutable, like variables.

To be able to modify it, we must add `mut` both to the variable and the reference. You can have only one mutable reference to a particular piece of data in a particular scope. This restriction prevents data races.

### The Slice Type

A string slice is a reference to part of a String, and it looks like `let hello = &s[0..5];`. Can also be done with arrays.

The type that signifies “string slice” is written as `&str`. String literals are slices 🤯

## Structs

A Struct is custom data type that lets you name and package together multiple related values:

```
struct User {
  username: String,
  email: String,
  sign_in_count: u64,
  active: bool,
}
```

After defining it we create an instance defining the values:

```
let user1 = User {
  email: String::from("someone@example.com"),
  username: String::from("someusername123"),
  active: true,
  sign_in_count: 1,
};
```

Fields can be accessed by dot notation. The entire instance can be mutable, but not only specific fields.

You can create an instance that uses most of another instance values by using struct update syntax:

```
let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    ..user1
};
```

You can use tuple structs: `struct Color(i32, i32, i32);`

### Derived traits

Adding stuff `#[derive(Debug)]` before a struct definition allows us to add more functionality. Rust provides a [number of traits](https://doc.rust-lang.org/book/appendix-03-derivable-traits.html) to be used with the `derive` annotation.

### Methods

A method is a function defined within the context of a struct, to add functionality related to the struct. They always receive _self_ as their first parameter:

```
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}
```

### Associated functions

We can define functions in `impl` that don't take `self` as a parameter. To call associated functions, use `::` syntax.

## Enums and Pattern Matching

### Enums

Enums are used to represent a _thing_ that can only be one of its variants (for example, an enum to work with IP addresses that can be v4 or v6). Used like:

```
enum IpAddr {
    V4(String),
    V6(String),
}

let four = IpAddr::V4(String::from("127.0.0.1"));
let six = IpAddr::V6(String::from("::1"));
```

Each variant can have different types and amounts of associated data, like `V4(u8, u8, u8, u8)` and `V6(String)`

Methods can also be defined for enums the same way as with structs, using `impl`

`Option` is an enum defined by the standard library that allows us to work with a value that could be something or it could be nothing. Rust doesn't have _null_.

```
enum Option<T> {
    Some(T),
    None,
}
```

### `match`

The `match` operator compares a value against a series of patterns and runs the code of the first pattern matched.

```
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

Match arms can bind to values, for example to work with the `Option<T>` enum mentioned before:

```
match x {
    None => None,
    Some(i) => Some(i + 1),
}
```

Matches need to cover all possible cases. `_` can be used as a placeholder for all the other cases we don't want/need to cover:

```
let some_u8_value = 0u8;
match some_u8_value {
    3 => println!("three"),
    _ => println!("other number");,
}
```

### `if let`

`match` can be a bit verbose if we only care about one case. In this cases we can use `if let`:

```
if let Some(3) = some_u8_value {
    println!("three");
} else {
    println!("other number");
}
```

## Packages, Crates and Modules

### Packages and Crates

A crate is a binary or library. A package is one or more crates that provide a set of functionality (a package contains a `Cargo.toml`). A package can _only_ contain 1 library. It can contain as many binary crates as you’d like, but it must contain at least one crate (either library or binary).

The crate root is either the `src/main.rs` or `src/lib.rs`. If a package contains src/main.rs and src/lib.rs, it has two crates: a library and a binary.

A package can have multiple binary crates by placing files in the src/bin directory (one file per crate).

### Modules

Modules are defined by `mod` keyword followed by the name of the module, with its contents wrapped in curly braces or, if the module is in its own file, as a statement: `mod module_name;`

A module tree :

```
crate
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment
```

To call a module function from another file we need to know its path. Absolute paths start from the crate root and use the crate name or a literal `crate`. Relative paths start from the current module and use `self` (to be used with `use`, explained later), `super` (would be like the `..` of a filesystem path), or an identifier in the current module.

All items in a module are private by default. You can expose them using `pub`. It must be added case per case, including the fields in a struct. Enums variants are public by default.

You can bring modules into the scope with `use`, so you don't have to call the whole path each time you want to use a function.

The idiomatic way to bring a function into scope is not to use the full path, so you can see where it's defined. However, with structs and enums, the full path is used _shrug_

If we’re bringing two items with the same name into scope we avoid to use the full path, or provide a new name with the `as` keyword:

```
use std::fmt::Result;
use std::io::Result as IoResult;
```

When we bring a name into scope with `use`, it's private. We can re-export it combining `pub use`

To bring multiple items from the same module, we can combine it into a single line like: `use std::{cmp::Ordering, io};`

With the glob operator `*` we can bring all the module items: `use std::collections::*;` (might not be a good idea as it makes it more difficult to know what is in the scope and where is defined)

## Concurrency

Concurrent programming: different parts of a program execute **independently**
Parallel programming: different parts of a program execute **at the same time**

To keep it simple the chapter refers to both as _concurrency_.

### Threads

Multiple threads can improve performance because the program does multiple tasks at the same time, but can also lead to problems as race conditions, deadlocks (where two threads are waiting for each other to finish using a resource the other thread has, preventing both threads from continuing), and other bugs.

Rust standard library only provides an implementation of 1:1 threading (the language calls the operating system APIs to create threads, meaning one operating system thread per one language thread). (I'm not really understanding the 1:1 threading vs M:N but I hope it will make sense in the future)

You can create a new thread using `thread::spawn` and pass it a closure containing the code we want to run in the new thread (closures are explained in Chapter 13 which I skipped YOLO).

```
use std::thread;
use std::time::Duration;

fn main() {
    thread::spawn(|| {
        for i in 1..10 {
            println!("hi number {} from the spawned thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    for i in 1..5 {
        println!("hi number {} from the main thread!", i);
        thread::sleep(Duration::from_millis(1));
    }
}
```

With this code the new thread will be stopped when the main thread ends, whether or not it has finished running, and there is no guarantee on the order in which threads run, or that the spawned thread will get to run at all.

`thread::spawn` returns a `JoinHandle`, an owned value that when we call the `join` method, it will wait for its thread to finish.

Calling `join` on the handle **blocks** the thread currently running.

Adding the `move` keyword before the closure forces it to take ownership of the values it's using.

```
let v = vec![1, 2, 3];

let handle = thread::spawn(move || {
    println!("Here's a vector: {:?}", v);
});

handle.join().unwrap();
```

### Transfer data between threads

#### Message passing

We can pass messages between threads using a _channel_. A channel has two parts: a transmitter and a receiver.

We create a new channel using `mpsc::channel`. `mpsc` stands for _multiple producer, single consumer_. It returns a tuple `(tx, rx)` (_transmitter_, _receiver_).

You can send messages using `tx.send(val)` (returns a `Result<T, E>`, so it returns an error if the receiving end has been dropped).

To receive messages, use `rx.recv()`, which blocks the main thread and waits for a value, or `rx.try_recv`, that doesn't block. Both return `Result<T, E>`.

`rx` can be treates as an iterator too, like:

```
for received in rx {
    println!("Got: {}", received);
}
```

You can clone the transmitter to be able to send messages from multiple threads to the same receiver: `let tx1 = mpsc::Sender::clone(&tx);`

#### Shared-state

Shared memory concurrency is like multiple ownership. This can be achieved using a _mutex_ (mutual exclusion). To access the data, a thread first asks to acquire the mutex _lock_ (a lock keeps track of who currently has exclusive access to the data). After using the data, you must unlock it.

To be able to have multiple ownership we need to Use `Arc<T>`, stands for _atomically reference counted_. It's similar to `Rc<T>`, but intended to be used in concurrent situations (comes with a performance cost, and we don't need it in single threads).

Warning! `Mutex<T>` comes with the risk of creating deadlocks.
