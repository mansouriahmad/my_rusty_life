
You can update Rust using :

```bash
Rustup update
```

You can tell Cargo to skip git using:

```bash
cargo new test_app --vcs
```


You can clean the build folder (called target) using:

```bash
cargo clean
```

**Note:** Four-space indentation is standard Rust style.

`isize` and `usize` types hold pointer-sized singed and unsigned integers, 32 bit long on 32-bit platforms, and 64 bit long on 64-bit platforms.

Why isize and usize both?
- Use usize when indexing collections or sizes (because indexes can't be negative).
- Use isize when you might subtract two pointers or offsets (since the result can be negative).


##### Assert! macro:
It checks that its argument is true and if not, it *panics*. Unlike C/C++, you cannot skip checking assertions in Rust. You need to use `debug_assert!` instead.


#### if statement

```Rust
if a > b {
    println!("A is greater than b");
} else {
    println!("A is NOT greater than b");
}
```
As you can see, it does not require `()` over the condition, but requires `{}`.


Rust infers the types of value only in the function body! It always requires the function return type and function parameters' types, explicitly!


In Rust, usually you return from a function with an expression that is **NOT** followed by a semicolon and use `return` statement only from explicit early returns from midst of a function.



```Rust
#[test]
foo() {
    assert_eq!(add_i32(12,12), 24)
}
```

You can use `Rust test` to run all tests you wrote which can be scattered throught your project.



**Reading Command-line:**

```rust
use std::str:FromStr;
use std::env;

fn main() {
    let mut numbers = Vec::new();
    for arg in env::args():skip(1) {
        numbers.push(u64::from_str(&arg)
                    .expect("error parsing argument");
    }

    if numbers.len() == 0 {
        eprintln!("Usaeg: number number");
        std::process::ext(1);
    }
}
```



**trait:** It is collection of methods that types can implement.



It is true that `vec` is supposed to be dynamic array! However, you still need to mark the variable `mut` for Rust to let you push numbers onto the end of it.



Rust iterators are efficient like they were written with a simple loop.


Rust does not have any exceptions! Instead, all errors are handled using either `Result` or `panic`.



When you use `vec` which is allocated on heap and its size is dynamic, Rust is cautious to leave the programmer in control of memory consumption, making it clear how long each value lives, while still ensuring memory is freed propmty when no longer needed.

For example in this code:

```Rust
let mut my_vevtor : Vec<i32> = vec![1,2,3,4,5];

for num in &my_vector[1..] {
    println!({}, *num);
    println!({}, num); //  the macro will automatically dereference it.
}
```


- In this example we are telling Rust that the ownership of the vector should remain with `my_vector`! We are just **borrowing** its element for the loop. The `&` operator borrows a reference to the vector's elements, letting num borrow each element in succession!


- `println!` takes a template string, and then substitutes formatted versions of the remaining arguments for the {...} forms as they appear in the template string, and writes the result to the standard output stream.


In Rust (unlike C/C++ which require `main` to return zero if the program finished successfully, or a non-zero exit status if something went wrong, Rust assumes that if `main` returns at all, the program finished successfully. You can explicity cause the program to terminate using functions like `expect` or `std::process::exit()`.


Run this command to open the rust documentation:

```bash
rustup doc --std
```



A Rust package whether a library or an executable, is called a `crate`.


If you define a particular version of a package in your toml file, you'd make sure that the code will continue to compile even if a newer version of the package are published!

Also note that crates can have optional features: part of the interface or implementation that not all users need, but that nonetheless make sense to include in that crate. For example **serde** crate offers ways to handle data from the web. But, it will be available only if we select the crate's `derive` feature:

```toml
[dependencies]
serde= = { version = "1.", features = ["derive"] }
```
We need to import only those crates that we'll use directly! Cargo takes care of bringing in whatever other crates need in turn! 



Rust **raw string** syntax:


```rust
let my_str = r###"Ahmad
Mansouri "ahmad" \n
"###
```

It is like the letter `r`, zero or more hash marks, a double quote, and then the contents of the string, terminated by another double quote followed by the same number of hash marks.


Usually `use` delcarations are added to the beginning of the file. However, it is not strictly necessary.

You can use `format!` macro to create you strings:

```rust
let response = format!("{} + {} = {}", 12 ,13, 25);
```


Rust makes sure that a shared data structure cannot be acceessed unless when you are holding the lock and release the lock automatically when you are done! 

If youre program compiles in Rust, it is free of data races.

All Rust functions are thread-safe.

```rust
enum Option<T> {
    None,
    Some(T),
}
```


**Documentation Commentt:** Use /// to mark the comment lines above the function definition. **Note** that rustdoc utility knows how to parse them, together with the code they describe, and produce online documentation.

```rust
fn parse<T: FromStr>(s: &str, operator: chat) -> Option<(T,T)> {
    match s.find(operator) {
        None => None,
        Some(index) => {
            match(T::from_str(&s[..index]), T::from_str(&s[index+1..])) {
                (Ok(l), Ok(r)) => Some((l,r)),
                _ => None
            }
        }
    }
}

#[test]
fn test_pase_pair() {
    assert_eq!(parse_pair::<i32>("",        ','), None);
    assert_eq!(parse_pair::<i32>("10,",     ','), None);
    assert_eq!(parse_pair::<i32>(",10",     ','), None);
    assert_eq!(parse_pair::<i32>("10,20",   ','), Some((10,20)));
    assert_eq!(parse_pair::<i32>("10,20xy", ','), None);
    assert_eq!(parse_pair::<i32>("0.5×",    '×'), None);
    assert_eq!(parse_pair::<i32>("0.5×1.5", '×'), Some((0.5, 1.5)));
}
```
A Rust programmer would call `T`  a `type parameter` of *parse_pair*.

When we use a generic function, Rust often are able to infer type parameters for us and we won't need to write them out as we did in the test code.

If you look at the match block, it will be resolved to (Ok(l), Ok(r)) only if both sides are evaluated to Ok.

```rust
_ => None
```

`_` is a wildcard pattern that matches anything and ignores its value.

If you want to take the value:


```rust
let x = 42;

match x {
    0 => println!("zero"),
    val => println!("something else: {val}"), // captures the value
}
```

It's common to initialize a struct's fields with variables of the same name, so rather than forcing you to write something like

```rust
MyClass { re: re, im : im }
```
Rust let's you simply write 

```rust
MyClass {re, im}.
```

You can access the first element of tuple variable `t` by `t.0`.

Rust, generally does not intend to convert between numeric types implicitly! Therefore, we need to write out the conversion we need:

```rust
t.0 as f64
```

**Important:** Fallible functions in Rustshould return a `Result` which is either `Ok(result)` on success, or `Err(e)` on failure where is an error code.

```rust
let output = match File::create(filename) {
    Ok(f) => f,
    Err(e) => {
        return Err(e);
    }
};
```

This kind of **match** statement is such a common pattern in Rust that the language provides the `?` operator as shorthand for the whole thing.

```rust
let output = File::create(filename)?;
```
If `File::create` fails, the `?` operator returns from write_image, passing along the error. Otherwise, `output` holds the successfylly opened File.


The `crossbeam` crate provides a number of valuable concurrency facilities, including a scoped thread facility.


Unlike functions declared with `fn`, we don't need to decalre the types of a closure's argument: Rust will infer them, along with its return type.


Use `bat` command line tool which replaces cat.


```rust
#[derive(Debug)]
struct Arguments {
    target: String,
    replacemnet: String,
    filename: string,
    output: string
}
```

`#[derive(Debug)]` attribute tells the compiler to generate some extra code that allows us to format the Arguments struct with `{:?}` in `println!`.


use `text-colorizer` for creating colourful output in the termial.


```rust
let data = match fs::read_to_string(&args.filename) {
    Ok(v) => v.
    Err(e) => {
        eprintln!("{} failed  to read from file '{}': {:?}",
                  "Error:".red().bold(), args.filename, e);
        std::process::exit(1);
    }
```

As you can see in the code, from a `match` statement, we could print the error into err stream as well as exiting the application in case it fails to read the file.

```rust
use regex::Regex;

fn replace(target: &str, replacement: &str, test: &str) 
    -> Result<String, regex::Error>
{
    let regex = Regex::new(target)?;
    Ok(regex.replace_all(text, replacement).to_string()
}
```
The function returns a `Result`. We use `?` to short-circuit in case `Regex::new` fails.




