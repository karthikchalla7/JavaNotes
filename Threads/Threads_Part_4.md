
## Advanced Threading Concepts: Locks and Semaphores

### Table of Contents
1. [Introduction](#introduction)
2. [Why Custom Locks](#why-custom-locks)
3. [Types of Custom Locks](#types-of-custom-locks)
4. [ReentrantLock](#reentrantlock)
5. [ReadWriteLock](#readwritelock)
6. [StampedLock](#stampedlock)
7. [Semaphores](#semaphores)
8. [Lock and Condition Interface](#lock-and-condition-interface)
9. [Inter-thread Communication](#inter-thread-communication)
10. [Interview Questions](#interview-questions)

### Introduction

This guide covers advanced threading concepts in Java, focusing on locks and semaphores. These mechanisms provide more flexible and powerful synchronization options compared to the basic `synchronized` keyword.

### Why Custom Locks

Until now, we have been using synchronized blocks which put a monitor lock on objects. However, there are limitations to this approach:

1. Monitor locks depend on objects.
2. If we have different objects accessing the same shared resource, synchronized blocks will allow both since they have different objects.
3. Sometimes we need only one thread to access the critical section, regardless of which object (same or different) the threads are using.

In these cases, synchronized blocks may not work effectively. This is where custom locks come in handy.

### Types of Custom Locks

The main types of custom locks that don't depend on objects are:

1. ReentrantLock
2. ReadWriteLock
3. StampedLock
4. Semaphore

## ReentrantLock

ReentrantLock is an alternative to synchronized blocks that offers more flexibility and features.

Key features:
- Ability to interrupt a thread waiting for a lock
- Ability to attempt to acquire a lock without blocking indefinitely
- Supports multiple condition variables
- Fairness policy option

Example:

```java
import java.util.concurrent.locks.ReentrantLock;

class SharedResource {
    private ReentrantLock lock = new ReentrantLock();
    private int count = 0;

    public void increment() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
}
```

## ReadWriteLock

ReadWriteLock allows multiple threads to read a resource concurrently while still providing exclusive access for write operations.

Key concepts:
- Shared lock (read lock): Multiple threads can hold simultaneously
- Exclusive lock (write lock): Only one thread can hold at a time

Important points:
- If any thread has put a shared lock on the resource, no other thread can acquire an exclusive lock.
- If an exclusive lock is acquired by any thread, no other thread can acquire a shared lock or another exclusive lock.

When to use:
- When reads are very frequent and writes are infrequent

Example:

```java
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

class SharedResource {
    private ReadWriteLock rwLock = new ReentrantReadWriteLock();
    private int data = 0;

    public int readData() {
        rwLock.readLock().lock();
        try {
            return data;
        } finally {
            rwLock.readLock().unlock();
        }
    }

    public void writeData(int newValue) {
        rwLock.writeLock().lock();
        try {
            data = newValue;
        } finally {
            rwLock.writeLock().unlock();
        }
    }
}
```

## StampedLock

StampedLock is an advanced locking mechanism that supports read and write locks, plus an optimistic read mode.

Key features:
- Three modes: Write, Read, and Optimistic Read
- Can upgrade from optimistic read to read lock
- Not reentrant (unlike ReentrantLock and ReentrantReadWriteLock)
- Provides both ReadWrite lock and ReentrantLock functionalities + optimistic read capabilities

Types of locks:
- Pessimistic: synchronized, ReentrantLock, ReadWriteLock
- Optimistic: StampedLock (in optimistic read mode)

## Pessimistic vs Optimistic Locking

### Pessimistic Locking

Pessimistic locking assumes that conflicts between concurrent operations are likely to occur. It acquires locks preemptively to prevent conflicts.

Characteristics:
- Locks are acquired before accessing the resource
- Ensures data consistency but may reduce concurrency
- Can lead to deadlocks if not managed properly
- Suitable for high-contention scenarios

Examples in Java:
- synchronized keyword
- ReentrantLock
- ReadWriteLock

How it works:
1. Thread acquires the lock
2. Thread accesses the shared resource
3. Thread releases the lock

Use case example:
Consider a banking system where multiple threads might try to modify an account balance simultaneously. Pessimistic locking would lock the account before any operation, ensuring that only one thread can modify the balance at a time.

### Optimistic Locking

Optimistic locking assumes that conflicts are rare. It allows concurrent access without locking but checks for conflicts before committing changes.

Characteristics:
- No locks are acquired initially
- Higher concurrency potential
- May require retry logic if conflicts occur
- Suitable for low-contention scenarios

Examples in Java:
- StampedLock (in optimistic read mode)
- Version numbers or timestamps for conflict detection

How it works:
1. Thread reads the resource and its version
2. Thread performs operations without locking
3. Before committing, thread checks if the version has changed
4. If unchanged, commit the changes; if changed, retry or handle the conflict

Example of optimistic locking scenario:
1. Time 1: T1 and T2 both want to access the same row in a database.
2. Both read the row, row version is 1 for both.
3. Time 2: T1 performs some operations.
4. Time 3: T2 updates the database, changing the row version to 2.
5. Time 4: T1 tries to update, but the update fails due to version mismatch.
6. T1 must then re-read the data with the new version and retry the operation.

Example:

```java
import java.util.concurrent.locks.StampedLock;

class SharedResource {
    private StampedLock sl = new StampedLock();
    private int data = 0;

    public int readData() {
        long stamp = sl.tryOptimisticRead();
        int currentData = data;
        if (!sl.validate(stamp)) {
            stamp = sl.readLock();
            try {
                currentData = data;
            } finally {
                sl.unlockRead(stamp);
            }
        }
        return currentData;
    }

    public void writeData(int newValue) {
        long stamp = sl.writeLock();
        try {
            data = newValue;
        } finally {
            sl.unlockWrite(stamp);
        }
    }
}
```

### Semaphores

Semaphores are used to control access to a shared resource by multiple threads.

Key concepts:
- Can be used to limit the number of threads accessing a resource simultaneously
- Binary semaphore (permits = 1) can be used as a mutex 

Use Cases:
1. Controlling access to a fixed number of resources (e.g., connection pools).
2. Synchronizing tasks in producer-consumer scenarios.
3. Implementing signaling between threads.  

Example:

```java
import java.util.concurrent.Semaphore;

class SharedResource {
    private Semaphore semaphore = new Semaphore(3); // Allow 3 threads at a time
    
    public void accessResource() throws InterruptedException {
        semaphore.acquire();
        try {
            // Access the shared resource
        } finally {
            semaphore.release();
        }
    }
}
```

### Lock and Condition Interface

The Lock interface provides more extensive locking operations than synchronized blocks. Condition allows threads to wait for specific conditions to be met.

Key methods:
- `lock()`: Acquires the lock
- `unlock()`: Releases the lock
- `newCondition()`: Creates a new Condition instance bound to this Lock instance

### Inter-thread Communication

For synchronized blocks, threads can communicate using `wait()`, `notify()`, and `notifyAll()` methods.

For custom locks, use Condition methods for inter-thread communication:
- `await()`: Similar to `wait()`
- `signal()`: Similar to `notify()`
- `signalAll()`: Similar to `notifyAll()`

These methods are called on Condition objects, which are bound to specific Lock instances.



## Interview Questions

1. Q: Why do we need custom locks when we have synchronized blocks?  
   A: Custom locks provide more flexibility, especially when we need to control access across different objects or implement more complex locking strategies.

2. Q: What is the difference between ReentrantLock and synchronized?  
   A: ReentrantLock offers more flexibility, including timed lock attempts, interruptible lock waits, fairness policy, and multiple condition variables.

3. Q: When would you use a ReadWriteLock instead of a ReentrantLock?  
   A: When you have a resource that is frequently read but infrequently modified, and want to allow multiple threads to read concurrently.

4. Q: What is the advantage of StampedLock over ReadWriteLock?  
   A: StampedLock provides an optimistic read mode, which can improve performance in read-heavy scenarios with infrequent writes.

5. Q: How does a Semaphore differ from a Lock?  
   A: A Semaphore can allow multiple threads to access a resource concurrently (based on the number of permits), while a Lock typically provides exclusive access.

6. Q: What is the purpose of the Condition interface?  
   A: Condition allows threads to wait for specific conditions to be met before proceeding, providing more fine-grained control than wait()/notify().

7. Q: How does StampedLock's optimistic read work?  
   A: It allows a thread to read without acquiring a lock, then later validate if the read was consistent. If not, it can acquire a full read lock.

8. Q: What is the difference between pessimistic and optimistic locking?  
   A: Pessimistic locking assumes conflicts will occur and locks resources preemptively. Optimistic locking assumes conflicts are rare and checks for conflicts only when updating.
