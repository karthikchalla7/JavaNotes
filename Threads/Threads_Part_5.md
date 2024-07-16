
## Lock-Free Programming and Atomic Variables in Java

This README provides an overview of lock-free programming concepts, Compare-and-Swap (CAS) operations, atomic variables, and related topics in Java concurrency.

### Table of Contents
1. [Lock-Free vs. Lock-Based Approaches](#lock-free-vs-lock-based-approaches)
2. [Compare-and-Swap (CAS) Operation](#compare-and-swap-cas-operation)
3. [ABA Problem in CAS](#aba-problem-in-cas)
4. [Atomic Variables](#atomic-variables)
5. [Atomic vs. Volatile](#atomic-vs-volatile)
6. [Java Code Examples](#java-code-examples)

### Lock-Free vs. Lock-Based Approaches
![Screenshot 2024-07-16 090024](https://github.com/user-attachments/assets/d68524a9-36e4-494f-91ea-de49895769d8)

- Lock-free programming is generally faster than lock-based approaches
- Lock-free is not a replacement for lock-based methods
- Lock-based is used for complex business logic
- Lock-free is suitable for specific use cases

### Compare-and-Swap (CAS) Operation

CAS is used to achieve lock-free concurrency and is a form of optimistic concurrency control.

### CAS Technique Overview
- Similar to optimistic concurrency control
- Low-level, atomic operation
- Supported by modern processors

### CAS Parameters
1. Memory location: where the variable is stored
2. Expected value: value expected to be in memory
3. New value: value to write if current value matches expected value

### CAS vs. Optimistic Concurrency Control
- CPU implements CAS operation
- Optimistic concurrency control is used in databases
- Optimistic concurrency control is inspired by CAS

### CAS Example
1. Memory location M1 contains variable X = 10
2. CAS operation uses three parameters: memory, expected value, new value
3. Load/read variable from M1
4. Compare stored data with expected value
5. Update to new value if match is found

### ABA Problem in CAS

- Initial value in M1 is 10
- CAS operation: CAS(M1, expected(10), newvalue(13))
- Thread 1 changes value to 12
- Another thread changes 12 back to 10
- CAS sees matching expected value (10) and updates to 13
- Issue: CAS doesn't detect intermediate changes

### ABA Solution
- Introduce version or timestamp for each value change
- Example:
    - 10 - version 1 (v1)
    - 12 - v2
    - 10 - v3 (different version)
- Expected value includes version (e.g., 10 with v1)
- No update if versions don't match

## Atomic Variables

### Atomicity Concept
- "All or Nothing" principle
- Single operations (e.g., int count = 10) are atomic
- Works well with single threads

### Non-Atomic Operations
- Example: count++ (three steps)
    1. Load counter value
    2. Increment by 1
    3. Assign back
- Not thread-safe or atomic

### Solutions for Thread Safety
1. Using locks (e.g., synchronized)
2. Lock-free operations with atomic integers (uses CAS)

### Atomic Variable Types
- AtomicInteger
- AtomicBoolean
- AtomicLong
- AtomicReference (for objects)
- Atomic can be used in
    - read
    - modify
    - update
- this is the simple use then we can use atomic.


## Atomic vs. Volatile

### Thread Safety
- Atomic variables are thread-safe
- Volatile variables are not thread-safe
- let say we have two core cpu core 1 and core2
- each cpu individual l1 cache for each core and l2 cache for the cpu then we have memory(Ram)
- let say t1 and t2 two threads and x =10
- core1(t1) adn core 2(T2) will have increment function and their respective caches(L1)
- now the t1 increment and change the x to 11
- now t2 before it increments it will try to access the value of 10 goes to l1 cache do you have value
- then it goes to l2 cache say i don't then goes to memory, and it gets the 11
- that is how the cache are in sync
- Let say they are not in sync now if it tires to read then it will have older value 10 not 11

- Now volatile make sures that read/write should happen from memory
- if the variable is volatile then it directly read and writes to the memory
- Any changes which was done by one thread will be visible to other thread
- It doesn't guarantee concurrency


### Memory Visibility
- Scenario: Two CPU cores with L1 and L2 caches, plus main memory (RAM)
- Threads T1 and T2 on different cores, variable X = 10

### Cache Synchronization Issues
- T1 increments X to 11
- T2 may read old value (10) from cache before synchronization

### Volatile Variables
- Ensure read/write operations happen directly from/to main memory
- Changes made by one thread are visible to other threads
- Don't guarantee concurrency

## Java Code Examples

### 1. AtomicInteger Example

```java
import java.util.concurrent.atomic.AtomicInteger;

public class AtomicIntegerExample {
    private static final AtomicInteger counter = new AtomicInteger(0);

    public static void main(String[] args) throws InterruptedException {
        Runnable task = () -> {
            for (int i = 0; i < 1000; i++) {
                counter.incrementAndGet();
            }
        };

        Thread t1 = new Thread(task);
        Thread t2 = new Thread(task);

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println("Final count: " + counter.get());
    }
}
```

This example demonstrates using AtomicInteger for a thread-safe counter.

### 2. AtomicBoolean Example

```java
import java.util.concurrent.atomic.AtomicBoolean;

public class AtomicBooleanExample {
    private static final AtomicBoolean flag = new AtomicBoolean(false);

    public static void main(String[] args) {
        new Thread(() -> {
            System.out.println("Thread 1: Waiting for flag to be set...");
            while (!flag.get()) {
                // Busy wait
            }
            System.out.println("Thread 1: Flag was set, proceeding...");
        }).start();

        new Thread(() -> {
            try {
                Thread.sleep(2000); // Simulate some work
                System.out.println("Thread 2: Setting flag to true");
                flag.set(true);
            } catch (InterruptedException e) {
                //some exception code here
            }
        }).start();
    }
}
```

This example shows how AtomicBoolean can be used for thread coordination.

### 3. AtomicReference Example

```java
import java.util.concurrent.atomic.AtomicReference;

class User {
    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{name='" + name + "', age=" + age + "}";
    }
}

public class AtomicReferenceExample {
    private static final AtomicReference<User> userRef = new AtomicReference<>(new User("Initial", 0));

    public static void main(String[] args) {
        new Thread(() -> {
            User oldUser = userRef.get();
            User newUser = new User("Alice", 25);
            if (userRef.compareAndSet(oldUser, newUser)) {
                System.out.println("Thread 1: Updated user to " + newUser);
            } else {
                System.out.println("Thread 1: Update failed");
            }
        }).start();

        new Thread(() -> {
            User oldUser = userRef.get();
            User newUser = new User("Bob", 30);
            if (userRef.compareAndSet(oldUser, newUser)) {
                System.out.println("Thread 2: Updated user to " + newUser);
            } else {
                System.out.println("Thread 2: Update failed");
            }
        }).start();

        try {
            Thread.sleep(100); // Give threads time to complete
        } catch (InterruptedException e) {
            //exception goes here
        }

        System.out.println("Final user: " + userRef.get());
    }
}
```

This example demonstrates the use of AtomicReference for thread-safe updates to a reference type.

These examples showcase how atomic variables can be used to implement lock-free mechanisms in Java. They provide thread-safe operations without the need for explicit locking, which can lead to better performance in certain scenarios.

### Concurrent Collection
![Screenshot 2024-07-16 100037](https://github.com/user-attachments/assets/2b114d14-7319-4b0b-a3ce-958398daf9a4)
