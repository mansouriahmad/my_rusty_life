- Rust language is to some extent designed around its types! 

- Rust's memory and thread safety comes from the soundness of its type systems

- Although Rust doesn't promise it will represent things exactly as you've requested, it takes care to deviate from your request only when it's a reliable improvement.

**What does it mean: Rust lets you leave out or *elide*?**

Look at the code below:

```rust
fn build_vector() → Vec<i16> {
    let mut v : Vec<i16> = Vec::<i16>::new();
    v.push(10i16);
    v.push(20i16);
    v
}
```

The issue is that the code is cluttered and repetitive. Since the function's return type is Vec<i16>, ***V*** should be a Vec<16>. From that, it follows that each element of the vector should be an `i16`. This is exactly the type of reasoning Rust's type inference applies, allowing the programmer to instead write:



```rust
fn build_vector() → Vec<i16> {
    let mut v  = Vec::new();
    v.push(10);
    v.push(20);
    v
}
```

**Note:**
- Rust will generate the same machien code either way!
- Type inference gives back much of the legibility of dynamically types languages, while still catching errors at compile time!


**Note:**
- Rust's generic functions give the language a degree of the same flexibilty as ***duck typing** in Python, while still catching all type errors at compile time.


**Imporant:** Generic functions are just as efficient as their nongeneric counterparts:
- There is not inherent perforamnce advantage to be had from writing a specific `sum` function for each integer over writing a generic one that handles all integers.


|Type   | Description |Example                            |
|-----------------------------|------------------------------------|--------|
|i32 || -5i8, 0x400u16, 0o100i16, 20_999u64, b'*' (u8 byte literal)|
|isize,usize||-0b0101_0010isize|
|char| Unicode Character, 32-bit wide|'*', '\x7f', '\u{CA0}'|
|(char, u8, i32)| Tuple: mixed types allowed| ('%', 0x7f, -1)|
| () | "Unit" (empty tuple) | () |
| struct S { x: f32, y : f32} | Named-field struct | S { x: 120.0, y: 209.0} |
| struct T (i32, char) | Tuple-like struct | T(120, 'X') |
| struct E; | Unit-like struct; has no fields | E |
|enum Attend { OnTime, Late(u32) } | | Attend::Late(5), Attend::OnTime |
|Box<Attend> | Box: owning pointer to value in heap | Box::new(Late(15)) | 
|&i32, &mut i32 | Shared and mutable references: non-owning pointers that must not outlive their referent||
|String | UTF-8 string, dynamically sized | "Hello".to_string() |
|&str | Reference to str: non-owning pointer to UTF-8 text | "Ahmad", &s[0..12] | 
| &[u8], &mut [u8] | Reference to slice: reference to a portion of an array or vector, comprising pointer and length | &v[10.20]|
| &dyn Any, &mut dyn Read | Triat object: reference to any value that implements a given set of methods | value as &dyn Any, &mut file as &mut dyn Read |
|fn (&str) → bool | Pointer to function | str::is_empty |

<br>

**What is the difference between Unicode and utf-8:**

#### 🔤 Unicode

**Unicode** is a *character set*. It defines a universal standard for **what characters exist** and assigns each one a unique **code point**.

Examples:
- `'A'` → `U+0041`
- `'€'` → `U+20AC`
- `'😀'` → `U+1F600`

Think of Unicode as a big **dictionary** of all characters from all languages, emojis, symbols, etc.

---

#### 🧩 UTF-8

**UTF-8** stands for *Unicode Transformation Format - 8 bit*. It is a **character encoding** — it defines how to store or transmit Unicode characters using **bytes**.

Examples:
- `'A'` → Unicode: `U+0041` → UTF-8: `0x41` (1 byte)
- `'€'` → Unicode: `U+20AC` → UTF-8: `0xE2 0x82 0xAC` (3 bytes)

---

#### ⚖️ Analogy

| Concept     | Analogy                                                     |
|-------------|-------------------------------------------------------------|
| Unicode     | A list of all books in a library (each with a unique ID)    |
| UTF-8       | How the books are stored on shelves (in binary form)        |

---

#### ✅ Summary Table

| Feature       | Unicode                        | UTF-8                             |
|---------------|--------------------------------|-----------------------------------|
| What it is    | Character set (abstract)       | Encoding format (concrete)        |
| Defines       | Code points                    | Byte sequences                    |
| Scope         | Universal                      | One of many encodings for Unicode |
| Byte size     | N/A                            | Variable-length (1–4 bytes)       |
| Compatibility | N/A                            | Backward compatible with ASCII    |

<hr>


### Fixed-Width Numerric Types
Fixed-width numeric types can overflow or lose precision, but they are adequate for most applications and can be thousands of times faster than representations like **arbitrary-precision integers** and **exact rationals**.


Rust used `u8` type for byte values:
- reading data from a binary file
- reding data from a binary socket

Unlike C and C++, Rust treats characters as distinct from the numeric types: a `char` is not a `u8`, nor it is `u32` (although it is 32 bits long).


The `usize` and `isize` types are analogous to `size_t` and `ptrdiff_t` in C and C++.

Rust requires array indices to be `usize` values.

---

**What happens if an integer literal lacks a type suffix?** <br>
Rust will attemp to infer its type until it find the value was used in a way that pins it down:
- stored in variable of a particular type
- passed to function that expects a particular type
- compared with another value of a particular type

In the end, if multiple types could work Rust defaults to `i32` if that is among the possibilities. Otherwise, Rust reports the ambiguity as an error.


----

##### Byte literal
Although numeric types and the `char` type are distinct, Rust does provide *`byte` literals*, character like literals for `u8` values: b'X' represents the ASCII code for the character X, as a `u8` value. For example, since the ASCII code for A is 65, the literals b'A' and 65u8 are exactly equivalent.

- Only ASCII characters may appear in byte literals.



Table: Characters requiring a stand-in notation

|Character|Byte literal|Numeric equivalent|
|---------|------------|------------------|
|Singe Quite, '| b'\\''|39u8|
|Backslash, \ | b'\\'| 92u8|
|Newline| b'\n'|10u8|
|Carriage return|b'\r'| 13u8|
|Tab|b'\t'|9u8|


For characters that are hard to write or read, you can write their code in hexadecimal instead. A byte literal of the form `b'\xHH'`, where HH is any two-digit hexadecimal number, represents the byte whose value is HH. For example, you can write a byte literal for ASCII "escape" control character as `b'\x1'`, since the ASCII code for "escape" is 27 or 1B in hexadecimal. Since byte literal are just another notation for `u8` values, consider whether a simple numberic literal might be more legible.


##### Integer Conversion
You can convert from one integer type to another using the `as` operator.

```rust
//Conversion that are out of range for the destination
// produce values that are equivalent to the original modulde 2^N,
// where N is the width of the destination in bits. This is sometimes
// called "truncations"
assert_eq!(1000_i16 as u8, 232_u8);
assert_eq!(65535_u32 as i16, -1_i16);

assert_eq!(-1_i8 as u8, 255_u8);
assert_eq!(255_u8 as i8, -1_i8);
```

The standard library provides some operations as methods on integers:

```rust
assert_eq!(2_u16.pow(4), 16);
assert_eq!((-4_i32).abs(), 4);
assert_eq!(0b101101_u8.count_ones(), 4);
```


---

**Question: Does the code below compile?**

```rust
println!("{}", (-4).abs());
```

It would not compile! Rust wnats to know exactly which integer type a value has before it will call the type's own methods. The default of `i32` applies only if the type is still ambiguous after all method calls have been resolved, so that's too late to help here.

**What is the solution then?**

```rust
println!("{}", (-4_i32).abs());
println!("{}", i32::abs(-4));
```

---

**Question: When an integer artithmatic operation overflows, what happens?**
- In debug build, Rust will panic!
- In release build, the operation *wraps around*: it produces the value equivalent to the mathematically correct result module the range of the value.

---

#### Enhanced Integer Operations
When the default behavior isn't what you need, the integer type provide methods that let you spell out exactly what you want.


##### *Checked* Integer Operations
They return an `Option` of the result: `Some(v)` of the mathematically correct can be represented as a value of that type, or `None` if it cannot.

```rust
assert_eq!(10_u8.checked_add(20), Some(30));
assert_eq!(100_u8.checked_add(200), None);

// Do the addition; panic if it overflows
let sum = x.checked_add(y).unwrap();
```

##### *Wrapping* Integer Operations
Return the value equivalent to the mathematically correct result modulo the range of the value:

```rust
assert_eq!(500_u16.wrapping_mul(500), 53392);

assert_eq!(500_i16.wrapping_mul(500), -12144);

// In bitwise shift operations, the shift distance
// is wrapped to fall within the size of the value.
// So a shift of 17 bits in a 16-bit type is a shift of 1
assert_eq!(5_i16.wrapping_shl(17), 10);
```

**NOTE:** The advantage of these methods is that they behave the same way in all builds.




##### *Saturating* Integer Operations
Return the representable value that is closest to the mathematically correct result (the result will be clamped).

```rust
assert_eq!(32760_i16.saturating_add(10), 32767);
assert_eq!((-32760_i16).saturating_sub(10), -32768);
```

**NOTE:** There are no saturating division, remainder, or bitwise shift methods.


##### *Overflowing* Integer Operations
Return a tuple `(result, overflowed)`, where result is what the wrapping version of the function would return and `overflowed` is a `bool` indicating whether an overflow occurred:

```rust
assert_eq!(255_u8.overflowing_sub(2), (253, false));
assert_eq!(255_u8.overflowing_add(2), (1, tur));
```

Overflowing _shl and overflowing_shr deviated from the pattern a bit: they return true for overflowed only if the shift distance was as large or larger than the bit width of the type itself:

```rust
assert_eq!(5_u16.overflowing_shl(17), (10, true));
```


#### Floating-Point
Every part of a floating-point number after the integer is optional, but at least one of the fractional part, exponent or type suffix must be present, to distiguish it from an integer literal. The fractional part may consist of a lone decimal point, so `5.` is a valid floating-point constant.


The types `f32` and `f64` have associated constants for the IEEE-required special values like `INFINITY`, `NEG_INFINITY`, `NAN` and `MIN` and `MAX`.


```rust
assert_eq!((-1. / f32::INFINITY).is_sign_negative());
assert_eq!(-f32::MIN, f32::MAX);

assert_eq!(f32.sqrt() * f32.sqrt(), 5.); // Exactly 5.0, per IEEE
```

The `std::f32::consts` and `std::f64::consts` modules provide various commonly used mathematical constants like `E`, `PI` and the square root of two.



#### bool

Rust is very strict: control structures like if and while require their conditions to be bool expressions, as do the short-circuiting logical operators && and ||

Rust cibvert bool values to integer types:

```rust
assert_eq!(false as i32, 0);
assert_eq!(true as i32, 1);
```

**Note:** Rust won't convert a numeric type to `bool`.

#### Characters
