# Chapter 3: Ownership and Moves 

Rust is something between "Safety First" where a garbage collection is used and "Control First" where program's memory consumption is entirely in programmer's control.

**Note:**
- Relying on garbage collection means relinquishing control over exactly when objects freed to the collector 
- Understanding thy memory wasn't freed when you expected, can be a challenge.


A bug in a Rust program cannot cause one thread to corrupt another's data, introducting hard-to-reproduce failures in unrelated parts of the system.


## Ownership
A `std::string` own its buffer: when the program destroys the string, the string's destructor frees the buffer.

The owner of a data determines the lifetime of the owned and everyone else must respect its decisions.

- Every value has a single owner that determines its lifetime.
- When the owner is freed - dropped, in Rust terminology - the owned value is dropped too.


Just as variables own their values, structs own their fields, and tuples, arrays and vectors own their elements.


The way to drop a value in Rust is to remove it from the ownership tree somehow:
- Leaving the scope of variables
- Deleting an element from a vectors
- Something of that sort 


Very simple types like integers, floating point numbers, and characters are excuses from the ownership rules. They are called `Copy` types.


These operations _moves_ the value instead of copying them:
- assigning a value to a variable
- passing it to a function
- returning it from a functio

It means that source relinquishes ownership of the value to the destination and becomes uninitialized.

#### A quick comparison between C++ and Rust

Let's say you have a list of strings in a variable and then you assign it into another variable.

```CPP
using namespace std;

vector<string> s = {"udon", "ramen", "soba"};
vector<string> t= s;
vector<string> u = s;
```

In C++, a deep copy will occur. On stack you'll have `s`, `t`, `u`, and their data will copied into heap.

```Rust
let s = vec!["udon".to_string(), "ramen".to_string(), "soba".to_string()];
let t = s;
let u = s; // Error since s is already moved and is uninialized.
```

In Rust, the data on heap (representing the strings) will be moved to `t`. Now, `t` is pointing the data on heap and `s` is uninitialize. Therefore the thrird command will be an error!

If you want to end up the same state as C++, you need to use `Clone`:

```Rust
let s = vec!["udon".to_string(), "ramen".to_string(), "soba".to_string()];
let t = s.Clone();
let u = s.Clone();
```

#### Some examples with move and control flow:

```Rust
let x = vec![1,2,3];
if c {
    f(x); // ... ok to move from x here
} else {
    g(x); // ... and ok to also move from x here
}

h(x); // bad: x is uninitialized here if either path uses it
```
```rust
let x = vec[1,2,3];
while f() {
    g(x); // bad: x would be moved in first operations
          // uninitialized in second...
}

```

remedy for the previous code:

```rust
let x = vec[1,2,3];
while f() {
    g(x);
    x = h();
}

e(x);
```


**Question:** What if we really do want to move an element out of a vector?

```rust
let mut v = Vec::new();
for i in 101..106 {
    v.push(i.to_string());
}

// 1. Pop a value off the end of the vector
let fifth = v.pop().expect("vector empty");
assert_eq!(fifth, "105");

// 2. move a value out of a given index in the vector,
// and move the last element into its spot.
let second = v.swap_remove(1);
assert_eq!(second, "102");

// 3. swap in another value of the one we're taking out:
let third = std::mem::replace(&mut v[2], "substitude".to_string());
assert_eq!(third, "103");

assert_eq!(v, vec!["101", "104", "substitude"]);
```


Now we need to analyze the code below:

```rust
let v = vec!["ahmad".to_string(), "sholeh".to_string()];

for mut s in v {
    s.push('!');
    println!("{s}");
}
```

- We are passing the vector directly therefore this moves vector out of v, leaving v uninialized
- The `for` loop internal machinery takes ownership of the vecto and dissects it into its element
- On each iteration, s owns the string, therefore we're able to modify it inside the loop- The vecotr itslef is no longer visible to the code and nothing can observe it mid-loop in some partially emptied state

---

Now, how would change a type so that in a vector or another collection you can track the presence/absence of a value? Probably using `Option`. Right?

```rust
struct Person {
    name: Option<String>,
    birth: i32
}

let mut composers = Vec::new();
composers.push(Person {
        name: Some("Palestrina".to_string()),
        birth: 1525
    });
```

We can't do: 

```rust
let first_name = composers[0].name;
```

Instead we can:

```rust
let first_name = std::mem::replace(&mut composers[0].name, None);
```

The code above (using `Option`) is so common that the type provides a `take` method for this very purpose:

```rust
let first_name = composers.[0].name.take();
```


### Copy Type 


The standard `Copy` types include all teh machine integer and floatint-point numeric types, the `char` and `bool` types and a few others. A tupe of fixed-size array of `Copy` types is itself a `Copy` type.

#### Rc and Arc

```rust
use std::rc::Rc;

let s: Rc<String> = Rc::new("ahmad".to_string());
let t: Rc<String> = s.clone();
let u: Rc<String> = s.clone();
```
- s, t, and u are located on the stack frame each pointing to the Rc and its value.
- Cloning an Rc<T> value does not copy the T; instead, it simply creates another pointer to it and increments the reference count.
- The usual ownership rules does apply to the `Rc` pointers themeselves, and when the last extant `Rc` is dropped, Rust drops the `String` as well.
- You can use any of `String`'s usual methods directly on `Rc<string>`


A value owned by an `Rc` is immutable.


**Note:** One well-known problems with using reference counts to manage memory is that, if there are ever two reference-count values that point to each other, each will hold the other's reference count above zero, so the values never will be freed.
