The author recommends doing side project when reading the book!

Look at Chris Biscardi's video on Youtube.

Traits are:
- Like interfaces in C++ and Java
- The main way Rust supports integrating your types into the language itself


You can find the github page of the book [here](https://github.com/ProgrammingRust)


What does code do in C?

```C
int main(int argc, char **argv) {
    unsigned long a[1];
    a[3] = 0x7ffff;
    return 0;
}
```

This code is somehow categorized as undefined behavior in C!


**What is the undefined behavior?**
- Behavior, upon use of a nonportable or erroneous program construct or of erroneous data, for which this International Standarad imposes no requirements.
- Underfined behavior doesn't just have an unpredictable result: the standard explicitly permits the program to do anything at all.

**Note:** Any software that might handle data from an untRusted source could be the traget of an exploit:
    - Look at the TrueType font issue in 2010.


**Rust Promise:** If a your program passes compiler's checks, it is free of undefined behavior!


**Concurrency:** Rust allows to easily share data betweeen threads, AS LONG AS it is NOT changing! Data that does change, can only be accessed using Synchronization primitives.

- [ ] Read a bit more about the **Read-Copy-Update**

<br>
<br>

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




**isize and usize** types hold pointer-sized singed and unsigned integers, 32 bit long on 32-bit platforms, and 64 bit long on 64-bit platforms.


Why isize and usize both?
- Use usize when indexing collections or sizes (because indexes can't be negative).

- Use isize when you might subtract two pointers or offsets (since the result can be negative).


##### Assert! macro:
It checks that its argument is true and if not, it *panics*. Unlike C/C++, you cannot skip checking assertions in Rust. You need to use *debug_assert!* instead.o


#### if statement

```Rust
if a > b {
    println!("A is greater than b");
} else {
    println!("A is NOT greater than b");
}
```
As you can see, it does not require () over the condition, but requires {}.<D-s>

<br>
<br>

Rust infers the types of value only in the function body! It always requires the function return type and function parameters' types, explicitly!

<br>
<br>

In Rust, usually you return from a function with an expression that is *not* followed by a semicolon and use **return** statement only from explicit early returns from midst of a function.


<br>
<br>

```Rust
#[test]
foo() {
    assert_eq!(add_i32(12,12), 24)
}
```

You can use **Rust test** to run all tests you wrote which can be scattered throught your project.

<br>
<br>

**trait:** It is collection of methods that types can implement.


<br>
<br> 

It is true that vec<i32> is supposed to be dynamic array! However, you still need to mark the variable **mut** for Rust to let you push numbers onto the end of it.


<br>
<br>

Rust iterators are efficient like they were written with a simple loop.

<br>
<br>

Rust does not have any exceptions! Instead, all errors are handled using either **Result** or **panic**.


<br>
<br>

When you use ***vec*** which is allocated on heap and its size is dynamic, Rust is cautious to leave the programmer in control of memory consymption, making it clear how long each value lives, while still ensuring memory is freed propmty when no longer needed.

For example in this code:

```Rust
let mut my_vevtor : Vec<i32> = vec![1,2,3,4,5];

for num in &my_vector[1..] {
    println!({}, *num);
    println!({}, num); //  the macro will automatically dereference it.
}
```


In this example we are telling Rust that the ownership of the vector should remain with my\_vector! We are just **borrowing** its element for the loop. The ***&*** operator borrows a reference to the vector's elements, letting num borrow each element in succession! **Note:** I think &my_vector will be something like a fat pointer with start and begin offset!


<br>
<br>

***println!*** takes a template string, and then substitutes formatted versions of the remaining arguments for the {...} forms as they appear in the template string, and writes the result to the standard output stream.

<br>
<br>

In Rust (unlike C/C++ which require *main* to return zero if the program finished successfully, or a non-zero exit status if something went wrong, Rust assumes that if ***main*** returns at all, the program finished successfully. You can explicity cause the program to terminate using functions like **expect** or **std::process::exot**.

<br>
<br>

Run this command to open the rust documentation:

```bash
rustup doc --std
```


<br>
<br>

A Rust package whether a library or an executable, is called a ***crate***.

<br>
<br>

If you define a particular version of a package in your toml file, you'd make sure that the code will continue to compile even if a newer version of the package are published!

Also note that crates can have optional features: part of the interface or implementation that not all users need, but that nonetheless make sense to include in that crate. For example **serde** crate offers ways to handle data from the web. But, it will be available only if we select the crate's **derive** feature:

```toml
[dependencies]
serde= = { version = "1.", features = ["derive"] }
```
We need to import only those crates that we'll use directly! Cargo takes care of bringing in whatever other crates need in turn! 

<br>
<br>


Rust **raw string** syntax:


```rust
let my_str = r###"Ahmad
Mansouri "ahmad" \n
"###
```

It is like the letter r, zero or more hash marks, a double quote, and then the contents of the string, terminated by another double quote followed by the same number of hash marks.

<br>
<br>

Usually **use** delcarations are added to the beginning of the file. However, it is not strictly necessary.

<br>
<br>

You can use **format!** macro to create you strings:

```rust
let response = format!("{} + {} = {}", 12 ,13, 25);
```


<br>
<br>

Rust makes sure that a shared data structure cannot be acceessed unless when you are holding the lock and release the lock automatically when you are done! 

If youre program compiles in Rust, it is free of data races.

All Rust functions are thread-safe.
<br>
<br>


```rust
enum Option<T> {
    None,
    Some(T),
}
```


<br>
<br>

**Documentation Commentt:** Use /// to mark the comment lines above the function definition. **Note** that rustdoc utility knows how to parse them, together with the code they describe, and produce online documentation.

<br>
<br>

```rust
fn parse<T: FromStr>(s: &str, operator: chat) -> Opttion<(T,T)> {
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
A Rust programmer would call T a ***type parameter*** of *parse_pair*.

When we use a generic function, Rust often are able to infer type parameters for us and we won't need to write them out as we did in the test code.

If you look at the match block, it will be resolved to (Ok(l), Ok(r)) only if both sides are evaluated to Ok.

```rust
_ => None
```

***_*** is a wildcard pattern that matches anything and ignores its value.

It's common to initialize a struct's fields with variables of the same name, so rather than forcing you to write something like MyClass { re: re, im : im }, Rust let's you simply write MyClass {re, im}.

<br>
<br>

You can access the first element of tuple variable t by t.0 .

<br>
<br>

Rust, generally does not intend to convert between numeric types implicitly! Therefore, we need to write out the conversion we need:

```rust
t.0 as f64
```

<br>
<br>

**Important:** Fallible functions in Rustshould return a ***Result*** which is either Ok(result) on success, or Err(e) on failure where is an error code.

<br>
<br>

```rust
let output = match File::create(filename) {
    Ok(f) => f,
    Err(e) => {
        return Err(e);
    }
};
```

This kind of **match** statement is such a common pattern in Rust that the language provides the **?** operator as shorthand for the whole thing.

```rust
let output = File::create(filename)?;
```
If File::create fails, the ? operator returns from write_image, passing along the error. Otherwise, *output* holds the successfylly opened File.

<br>

The crossbeam crate provides a number of valuable concurrency facilities, including a scoped thread facility.

<br>

Unlike functions declared with fn, we don't need to decalre the types of a closure's argument: Rust will infer them, along with its return type.


<br>

Use ***bat*** command line tool which replaces cat.

<br>

```rust
#[derive(Debug)]
struct Arguments {
    target: String,
    replacemnet: String,
    filename: string,
    output: string
}
```

#[derive(Debug)] attribute tells the compiler to generate some extra code that allows us to format the Arguments struct with {:?} in println!.

<br>

use **text-colorizer** for creating colourful output in the termial.

<br>

```rust
let data = match fs::read_to_string(&args.filename) {
    Ok(v) => v.
    Err(e) => {
        eprintln!("{} failed  to read from file '{}': {:?}",
                  "Error:".red().bold(), args.filename, e);
        std::process::exit(1);
    }
```


As you can see in the code, from a ***match*** statement, we could print the error into err stream as well as exiting the application in case it fails to read the file.


<br>

```rust
use regex::Regex;

fn replace(target: &str, replacement: &str, test: &str) 
    -> Result<String, regex::Error>
{
    let regex = Regex::new(target)?;
    Ok(regex.replace_all(text, replacement).to_string()
}
```

The function returns a Result. We use ? ti short-circuit in case Regex::new fails.



#### Chaper 3: Fundamental Types



