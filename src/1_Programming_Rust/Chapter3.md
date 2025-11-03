- Rust language is to some extent designed around its types! 

- Rust's memory and thread safety comes from the soundness of its type systems and its flexibility stems from its generic types and traits.

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

The issue is that the code is cluttered and repetitive. Since the function's return type is `Vec<i16>`, `V` should be a `Vec<i16>`. From that, it follows that each element of the vector should be an `i16`. This is exactly the type of reasoning Rust's type inference applies, allowing the programmer to instead write:



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
- Rust's generic functions give the language a degree of the same flexibilty as `duck typing` in Python, while still catching all type errors at compile time.


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

#### Byte literal
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


#### Integer Conversion
You can convert from one integer type to another using the `as` operator.

```rust
//Conversion that are out of range for the destination
// produce values that are equivalent to the original modulo 2ⁿ
// where N is the width of the destination in bits. This is sometimes
// called "truncations"
assert_eq!(1000_i16 as u8, 232_u8);
assert_eq!(65535_u32 as i16, -1_i16);
println!("Hey");
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




#### *Saturating* Integer Operations
Return the representable value that is closest to the mathematically correct result (the result will be clamped).

```rust
assert_eq!(32760_i16.saturating_add(10), 32767);
assert_eq!((-32760_i16).saturating_sub(10), -32768);
```

**NOTE:** There are no saturating division, remainder, or bitwise shift methods.


#### *Overflowing* Integer Operations
Return a tuple `(result, overflowed)`, where result is what the wrapping version of the function would return and `overflowed` is a `bool` indicating whether an overflow occurred:

```rust
assert_eq!(255_u8.overflowing_sub(2), (253, false));
assert_eq!(255_u8.overflowing_add(2), (1, true));
```

`overflowing_shl` and `overflowing_shr` deviated from the pattern a bit: they return true for overflowed only if the shift distance was as large or larger than the bit width of the type itself:

```rust
assert_eq!(5_u16.overflowing_shl(17), (10, true));
```


#### Floating-Point
Every part of a floating-point number after the integer is optional, but at least one of the fractional part, exponent or type suffix must be present, to distiguish it from an integer literal. The fractional part may consist of a lone decimal point, so `5.` is a valid floating-point constant.


The types `f32` and `f64` have associated constants for the IEEE-required special values like `INFINITY`, `NEG_INFINITY`, `NAN` and `MIN` and `MAX`.


```rust
assert!((-1. / f32::INFINITY).is_sign_negative());
assert_eq!(-f32::MIN, f32::MAX);

assert_eq!(5f32.sqrt() * 5f32.sqrt(), 5.); // Exactly 5.0, per IEEE
```
#### Why this is true in Rust?

This is a great example of how **floating-point arithmetic** and **rounding** behave in Rust (and IEEE-754 floats in general).

You might expect floating-point rounding errors to make this *false* (e.g. `4.9999995 ≠ 5.0`),  
but it actually passes. Let’s see why.

🧠 Step 1: What Happens Numerically

- `5f32.sqrt()` computes the square root of 5 as a `f32`.  
  In decimal, that’s approximately:

  ```
  √5 ≈ 2.236068
  ```

- Multiplying it by itself:
  ```
  2.236068 * 2.236068 ≈ 5.0000005
  ```

That seems slightly off, but not enough to fail equality.


⚙️ Step 2: Floating-Point Precision in `f32`

`f32` (32-bit float) has about **7 significant decimal digits** of precision.

When you multiply two `f32` values, the result is **rounded** to the nearest representable 32-bit float.  
That rounding can make the result *exactly* 5.0, even if the true mathematical value was 5.0000005.

Example (conceptually):

```
sqrt(5f32) ≈ 2.23606801033  (stored as a 32-bit float)
2.23606801033 * 2.23606801033 = 5.000000476837158
```

Then, because `5.000000476837158` is **closer to 5.0 than to the next representable float**,  
it gets **rounded to exactly 5.0** in `f32` precision.

So:

```rust
5f32.sqrt() * 5f32.sqrt() == 5.0f32
```

🧪 Step 3: Verifying in Code

```rust
fn main() {
    let x = 5f32.sqrt();
    println!("{:?}", x * x); // prints "5.0"
    println!("{:?}", (x * x).to_bits()); // prints "1084227584"
    println!("{:?}", 5f32.to_bits());     // prints "1084227584"
}
```

Both values have the **exact same bit pattern** (`0x40A00000`),  
so they are *bitwise identical* — the `assert_eq!` passes.

⚠️ Step 4: But Not Always True

For other numbers, rounding can go the *other way*.  
For example:

```rust
assert_ne!(3f32.sqrt() * 3f32.sqrt(), 3.0);
```

This fails, because the result is slightly below 3.0 after rounding.

---
✅ Summary

| Expression | Result | Equal to 5.0? | Why |
|-------------|---------|----------------|------|
| `5f32.sqrt() * 5f32.sqrt()` | `5.0` | ✅ Yes | rounds exactly to 5.0 |
| `3f32.sqrt() * 3f32.sqrt()` | `2.9999998` | ❌ No | rounding error slightly below |

---

💡 In Practice

Always use **approximate comparison** for floats:

```rust
assert!((a - b).abs() < 1e-6);
```

Only use `assert_eq!` when you know the result is *exactly representable* —  
as in this rare case with `5f32`.


---



The `std::f32::consts` and `std::f64::consts` modules provide various commonly used mathematical constants like `E`, `PI` and the square root of two.


**Note:** Implict integer conversions havea well-established record of causing bugs and secuirty holes, epecially when the integer in question represent the size of something in memory, and an unanticipated overflow occurs.

#### bool

Rust is very strict: control structures like if and while require their conditions to be bool expressions, as do the short-circuiting logical operators && and ||

Rust convert bool values to integer types:

```rust
assert_eq!(false as i32, 0);
assert_eq!(true as i32, 1);
```

**Note:** Rust won't convert a numeric type to `bool`.

#### Characters

Rust character type `char` represents a single Unicode character, as a 32-bit value.

**Important:**
- Rust uses the `char` type for single characters in isolation.
- However, it uses the UTF-8 enconding for strings and streams of text. Therefore, a `String` represents its text as a sequence of UTF-8 bytes, not as an array of characters.


You can use '\xHH' format to write any asky character set (U+0000 to U+007F). Also, you can write any Unicode character as '\u{HHHHHH}', where HHHHHH is a hexadecimal number up to six digits long, with underscores allowed for grouping as usual.

 Rust never implicitly converts between `char` and any other type.

 **char to integer type conversion:**
 You can use `as` operator to convert a `char` to an integer type; for types smaller than 32 bits, the upper bit of the characterr's value are truncated:

 ```rust
 assert_eq!('*' as i32, 42);
 ```

**conversion to char:**
`u8` is the only type the `as` operator will convert to `char`. Why?
- Rust intends the `as` operator to perform only cheap, infallible conversions
- Every integer type other than `u8` includes values that are not permitted Unicode code points
- As a result, those conversion would require run-time checks.

**How to convert to `char` then?**
The standard library function `std::char::from_u32` takes any `u32` value and returns an `Option<char>`. If the `u32` is not a permitted Unicode code point, then `from_u32` returns `None`; Otherwise it returns `Some(c)`, where `c` is the `char` result.

Some more useful methods provided by standard library:

```rust
assert_eq!('*'.is_alphabetic(), false);
assert_eq!('β'.is_alphabetic(), true);
assert_eq!('8'.to_digit(10), Some(8));
assert_eq!('ج'.len_utf8(), 2);
assert_eq!(std::char:from_digit(2, 10), Some('2'));
```


#### Tuples

Tuples allow only constants as indices, like t.4. You can't write `t.i` or `t[i]` to get the ith element.

Rust code often uses tuple types to return multiple values from a function.

```rust
fn split_at(&self, mid: usize) -> (&str, &str);
```


One commonly used tuple type is the zero-tuple `()`. This is traditionally called _unit type_ because it has only one value, also written `()`. Rust uses the tye `unit` type where there's no meaningful value to carry, but context require some sort of type.

| Situation                 | Why `()` is used                         |
|----------------------------|------------------------------------------|
| Function returns nothing   | Return type must exist                   |
| Statements                 | All statements evaluate to `()`          |
| Closures with no return    | They still have a return type            |
| Match arms/branches        | Must have same type                      |
| Placeholder in generics    | Type needed, no value to carry           |


Rust consistently permits an extra trailing comma everywhere comma are used!

There ven tuples that contain a single value. The literal `("lonely hearts",)` is a tuple containing a single string; its type is `(&str,)`. Here the comma after the value is necessary to distinguish the singleton tuple from a simple parenthetic expression.


```rust
fn main() {
    let a = ("hello");    // &str, it is equal to let a = "hello";
    let b = ("hello",);   // (&str,)

    println!("{:?}", a);  // prints: hello
    println!("{:?}", b);  // prints: ("hello",)
}
```

#### Pointer Types

At runtime, a reference to an `i32` is a single machine word holding the address of the `i32` which may be on the stack or in the heap.

The expression `&x` produces a reference to x; in Rust terminology, we say that it _borrows_ a _reference_ to _x_.

Given a reference `r`, the expression `*r` refers to the value `r` points to.

A reference does not automatically free any resources when it goes out of the scope! Instead when the owner goes out of scope, the data will be cleared!

##### &T
This is an immutable shared reference. You can have many shared references to a given value at a time, but they are read-only. Modifying the value they point to is forbidden, as with `const T*` in C.

##### &mut T
A mutable and exclusive reference.


#### Boxes
The simplest way to allocate a value in the heap is to use `Box::new`

```rust
let t = (12, "eggs");
let b = Box::new(t);
```

In this code, when `b` goes out of scope, the memory is freed immediately, unless `b` has been moved - by returning it, for example.


#### Raw pointers
Rust has the raw pointer types `*mut T` and `*const T`. Using a raw pointer is unsafe, because Rust makes no effort to track what it points to. You may only dereference a raw pointer within an `unsafe` block. An `unsafe` block is Rust's opt-in mechanism for advanced language features whose safety is up to you. If your code has no `unsafe` block (or if those it does are written correctly), then the safety feature guarantees we emphasize throughout this book still hold.


#### Arrays, Vectors, and Slices

The types `&[T]` and `&mut [T]` called a _shared slices of Ts_ and _mutable slice of Ts_. A mutable slice lets you read and modify elements, but can't be shared; a shared slice lets you share access among several readers, but doesn't let you modify elements.

Given a value `v` of any of these types, the expression `v.len()` gives the number of elements in `v`. 


Rust has not notion for an initialized array.

**Note:** 
The useful methods you'd like to see on arrays, are all provided as methods on slices, not arrays. But, Rust implicity converts a reference to an array to a slice when searching for methods, you can call any slice method on an array directly


```rust
let mut chaos = [3,5,4,1,2];
chaos.sort();
assert_eq!(chaos, [1,2,3,4,5]);
```
The `sort` method is actually defined on slices, but since it takes its operand by reference, Rust implicity produces a `&mut [i32]` slice referring to the entire array and passes that to the `sort` method.
##### Vectors

```rust
let mut primes = vec![2,3,5,7]
assert_eq!(primes.iter().product::<i32>(), 210);
```


**Question:** Look at the code below. What happens if `rows * cols` cannot fit into a usize? How can we make it more resilient.

```rust
fn new_pixel_buffer(rows: usize, cols: usize) -> Vec<u8> {
    vec![0; rows * cols]
}
```

- In debug mode, Rust panics when arithmetic overflows.

- In release mode, Rust performs wrapping arithmetic, meaning the value will silently wrap around (like modulo 2^64 or 2^32), which leads to allocating far fewer bytes than expected, potentially causing a buffer overflow or logic bugs later.

```rust
fn new_pixel_buffer(rows: usize, cols: usize) -> Option<Vec<u8>> {
    rows.checked_mul(cols).map(|size| vec![0; size])
}
```




One way of making vectors is from values produced by an iterator:

```rust
let v: Vec<i32> = (0..5).collect();
assert_eq!(v, [0,1,2,3,4]);
```

If you know the number of elements a vector will need in advance, instead of `Vec::New()` you can call `Vec::with_capacity` to create a vector with a buffer large enough to hold them all, right from the start. 

You can insert and remove elements wherver you like in a vector, although these operations shift all the elements after the affected position forward or backward, so they may be slow if the vector is large:

```rust
let mut v = vec![1,2,3];

v.insert(3,35);
v.remove(1);
```

You can use the `pop` method to remove the last element and return it. Note that this method returns an `Option<T>`.

Despite ites fundamental role, `Vec` is an ordinary type defined in Rust, not built into the language.


**Example:** Read arguemnt values:

```rust
let languages: Vec<String> = std::env::args().collect();
for l in languages {
    println!("{l:?}");
}
```

#### Slices
A slice, written `[T]` without specifying the length, is a region of an array or vector.

- Since a slice can be any length, slices can't be stored directly in variables or passed as function arguments (note that you cannot have a variable of type `[T]` but you can have of type `&[T]`)

```rust
let noodle = "noodle".to_string();
let oodle = noodle[1..]; // this is an error
```
- Slices are always passed by reference.
- A reference to a slice is _fat pointer_.

While an ordinary reference is a non-owning pointer to a singled value, a reference to a slice is a non-owning pointer to a range of consecutive values in memory. This makes slice references a good choice when you want to write a function that opreates on either an array or a vector.

```rust


fn print(n: &[f64]) {
    for elt in n {
        println!("{}", elt);
    }
}

let a = [12., 13., 14.];
let v = vec![12., 13., 14.];
print(&a); // works on arrays
print(&v); // works on vectors
```

#### String

In string literals, unlike `char` literals, single quotes don't need a backslash escape, and double quotes do.


**Note:** If one line of a string ends with a backslash, then the newline character and the leading whitespace on the next line are dropped!

In a few cases, the need to double every backslash in a string is s nuisance (The classic examples are regular expressions and Windows paths). For those cases, Rust offers _raw strings_. A raw string is tagged with the lowercase `r`. All backslashes and whitespace characters inside a raw string are included in verbatim in the string. No escape sequence are recognized.

```rust
let default_win_install_path = r"C:\Program Files\Gorillas";
let pattern = Regex::new(r"\d+(\.\d+)*");
```

**Note:** There cannot be a double-quote character in a raw string simply by putting a backslash in front of it - We said _no_ scape sequences are recognized. However, there is a cure for that:

```rust
println!(r###"
    This raw string started with 'r###"'. Therefore it does not end until we reach a quote mark ('"')
    followed by immediately by thtree pound signs ('###'):
"###)
```


#### Byte Strings

```rust
let method = b"GET";
assert_eq!(method, &[b'G', b'E', b'T']);
```

Byte strings can use all the other strings syntax we've shown: They can span multiple lines, use escape sequencess, and use backslashes to join lines. Raw byte strings start with `br"`.


Byte strings can't contain arbitrary Unicode characters. They must make do with ASCII and \xHH escape characters.

**Question:** Where should we use Byte Strings?

In Rust, you’d use byte strings (b"...") when you want to work with raw bytes instead of UTF-8 &str. This is useful when you’re dealing with binary data, protocols, or non-UTF-8 text.

```rust
let s = b"hello"; // type is &[u8; 5]
```
- b"..." creates a byte string literal (&[u8]).
- Normal string "..." is UTF-8 (&str).
- Byte strings cannot contain Unicode characters outside 0–127.

Suppose you’re parsing a binary protocol where the first 4 bytes are a magic number:

```rust
let packet: &[u8] = &[0xDE, 0xAD, 0xBE, 0xEF, 1, 2, 3];

if packet.starts_with(b"\xDE\xAD\xBE\xEF") {
    println!("Valid packet header!");
} else {
    println!("Invalid packet header!");
}
```

#### Strings in Memory

Rust strings are sequences of Unicode characters, but they are not stored in memory as array of `chars`. Instead, they are stored using UTF-8, a variable-width encoding.

A `string` or `&str`'s `.len()` method returns its length. The length is measured in bytes, not characters.

```rust
let a = "احمد".to_string();
let l = a.len();
let c = a.chars().count();
println!("a's length = {l} and its char length = {c}");
```

It is **impossible** to modify a `&str`.

When a `String` variable goes out of scope, the buffer is automatically freeds, unless the `String` was moved.


There are several ways to create `Strings`:

- The `.to_string()` method converts a `&str` to a `String`. This copies the string.
- The `format!()` macro works just like `println!()`, except that it returns a new `String` instead of writing text to stdout, and it doesn't automatically addd a new line at the end.
- Arrays, slices, and vectos of strings have two methods, `.concat()` and `.join(sep)` that form a new `String` from many strings.

```rust
let bits = vec!["veni", "vidi", "vici"];
assert_eq!(bits.contact(), "venividivici");
assert_eq!(bits.join(", "), "veni, vidi, vici");
```
#### Using Strings

String support `==` and `!=` operators. Two strings are equal if they contain the same characters in the same order (regardless of whether they point to the same location in memory):

```rust
assert!("ONE".to_lowercase() == "one");
```

**Note:** Give the nature of Unicode, simple `char-by-char` comparison does not always give the expected answers. For example, the Rust string "th\u{e9}" and "the\u{301}" are both valid Unicode reprsenations for _thé_, the French work for tea. Unicode says they should both be displayed and processed in the same way, but Rust treats them as two completely distinct strings.




**IMPORTANT:**
Sometimes a program really needs to be able to deal with strings that are NOT valid Unicode. This usually happens when a Rust program has to interoperate with some other system that doesn't enforce any such rules. For example, in most operating systems it's easy to create a file with filename that isn't valid Unicode. What should happen when a Rust program comes across this sort of filenames?

Rust solution is to offer a few string-like types for these situations:

- Sitck to `String` and `&str` for Unicode text.
- When working with filenames, use `std::path:PathBuf` and `&Path` instead.
- When working with binary data file that isn't UTF-8 encoded at all, use `Vec<u8>` and `&[u8]`.
- When working with environment variable names and command=-line arguments in the native form represented by operating system, use `OsString` and `&OsStr`.
- When interoperating with C libraries that use null-terminated strings, use `std::ffi::CString` and `&CStr`.

