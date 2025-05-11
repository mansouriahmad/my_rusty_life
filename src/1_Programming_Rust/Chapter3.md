- Rust language is to some extent designed around its types! 

- Rust's memory and thread safety comes from the soundness of its type systems

- Although Rust doesn't promise it will represent things exactly as you've requested, it takes care to deviate from your request only when it's a reliable improvement.

**What does it mean: Rust lets you leave out or *elide*?**

Look at the code below:

```rust
fn build_vector() -> Vec<i16> {
    let mut v : Vec<i16> = Vec::<i16>::new();
    v.push(10i16);
    v.push(20i16);
    v
}
```

The issue is that the code is cluttered and repetitive. Since the function's return type is Vec<i16>, ***V*** should be a Vec<16>. From that, it follows that each element of the vector should be an `i16`. This is exactly the type of reasoning Rust's type inference applies, allowing the programmer to instead write:



```rust
fn build_vector() -> Vec<i16> {
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
|fn (&str) -> bool | Pointer to function | str::is_empty |

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



