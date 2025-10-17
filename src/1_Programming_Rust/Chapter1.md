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

**What is Read-Copy-Update**


In the realm of concurrent programming, ensuring that multiple threads can access shared data without causing corruption is a fundamental challenge. Traditional locking mechanisms, while effective, can introduce performance bottlenecks. The Read-Copy-Update (RCU) mechanism offers an elegant, lock-free approach to synchronization, particularly optimized for read-heavy workloads. It allows readers to access data without waiting for locks, while writers can modify data without blocking those readers.

Here's a breakdown of how this ingenious mechanism works:

## The Core Idea: Out with the Old, In with the New
At its heart, RCU operates on a simple yet powerful principle: instead of modifying data in-place, which would require locking to prevent readers from seeing inconsistent intermediate states, an updater creates a copy of the data, modifies the copy, and then atomically updates a global pointer to point to this new version.

This "copy-on-write" strategy ensures that readers, who are concurrently accessing the data, will always see a consistent snapshot of the data—either the old version or the new one, but never a partially modified state.

## The Three Phases: Read, Copy, and Update in Action
The name "Read-Copy-Update" neatly encapsulates the three key phases of its operation. Let's delve into each:

1. **Read Phase: Unfettered Access**  
Readers in an RCU-protected environment can access the shared data without acquiring any locks. They simply enter a read-side critical section, which is a lightweight mechanism to inform the system that they are actively reading. This is a key advantage of RCU as it makes read operations incredibly fast and scalable.

Inside this critical section, a reader obtains a pointer to the current version of the data. Because the original data is never modified directly, the reader is guaranteed to have a consistent view of it for the duration of its read operation.

2. **Copy and Update Phase: A Graceful Transition**  
When a thread needs to modify the shared data, it performs the following steps:

- **Create a Copy:** The updater first creates a new copy of the data structure it intends to modify.
- **Modify the Copy:** All modifications are then made to this new, private copy. Since no other thread is aware of this copy yet, no locks are needed.
- **Publish the Changes:** Once the modifications are complete, the updater atomically changes the global pointer to point to the new, updated copy of the data. Modern processors guarantee that this pointer update is an atomic operation, meaning it's seen as a single, indivisible step by all other threads.

From this point on, any new readers entering a read-side critical section will see the new version of the data.

## The Crucial Fourth Step: The Grace Period and Reclamation
A critical question arises: what happens to the old version of the data? It cannot be deallocated immediately because existing readers might still be using it. This is where the concept of a **grace period** comes into play.

A grace period is a waiting period that an updater must observe before it can safely reclaim the memory of the old data. This period is defined as the time it takes for all readers that were active at the time of the update to complete their read-side critical sections.

Here’s how it works:

- **Waiting for Pre-existing Readers:** After publishing the new version, the updater waits for a grace period to elapse.  
- **Quiescent State:** The system keeps track of which threads are in a read-side critical section. When a thread exits its critical section, it is said to be in a quiescent state.  
- **Reclamation:** Once all threads that were active during the update have passed through a quiescent state, the grace period is over. The updater now knows that no reader is holding a reference to the old data, and it can be safely deallocated.

It's important to note that the grace period mechanism doesn't need to wait for readers that started after the update was published, as those readers will only ever see the new version of the data.

In essence, RCU splits the update process into two key stages:

- **Removal:** The atomic pointer update that makes the old data unreachable for new readers.  
- **Reclamation:** The deallocation of the old data after a grace period.

This separation is what allows RCU to achieve its lock-free nature for readers, providing a highly efficient synchronization mechanism for read-dominant scenarios.

