# Chapter 1: Programing Rust
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
