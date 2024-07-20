## ScheduledThreadPool, Virtual Thrads and Local Threads

## Table of Contents
1. [ExecutorService Shutdown Methods](#executorservice-shutdown-methods)
   - [shutdown() vs shutdownNow() vs awaitTermination()](#shutdown-vs-shutdownnow-vs-awaittermination)
2. [ScheduledThreadPoolExecutor](#scheduledthreadpoolexecutor)
3. [ThreadLocal](#threadlocal)
4. [Virtual Threads vs Platform Threads](#virtual-threads-vs-platform-threads)
5. [Important Interview Questions](#important-interview-questions)

## ExecutorService Shutdown Methods

### shutdown() vs shutdownNow() vs awaitTermination()

#### shutdown()
- Initiates an orderly shutdown of the ExecutorService.
- After calling `shutdown()`, the executor will not accept new task submissions.
- Already submitted tasks will continue to execute.

```java
ExecutorService executor = Executors.newFixedThreadPool(5);
// ... submit tasks ...
executor.shutdown();
```

#### awaitTermination()
- Optional functionality that returns true or false.
- Used after calling the `shutdown()` method.
- Blocks the calling thread for a specific timeout period and waits for ExecutorService shutdown.
- Returns true if ExecutorService shuts down within the specified timeout, otherwise false.

```java
boolean terminated = executor.awaitTermination(60, TimeUnit.SECONDS);
```

#### shutdownNow()
- Best-effort attempt to stop/interrupt actively executing tasks.
- Halts the processing of tasks that are waiting.
- Returns a list of tasks that were awaiting execution.

```java
List<Runnable> pendingTasks = executor.shutdownNow();
```

## ScheduledThreadPoolExecutor

A child class of ThreadPoolExecutor that provides methods for scheduling tasks.

### Methods:

1. `schedule(Runnable command, long delay, TimeUnit unit)`
   - Schedules a runnable task after a specific delay.
   - Runs only once.

```java
ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);
scheduler.schedule(() -> System.out.println("Delayed task"), 5, TimeUnit.SECONDS);
```

2. `schedule(Callable<V> callable, long delay, TimeUnit unit)`
   - Schedules a callable task after a specific delay.
   - Runs only once.

```java
Future<String> future = scheduler.schedule(() -> "Delayed callable result", 5, TimeUnit.SECONDS);
```

3. `scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit)`
   - Schedules a runnable task for repeated execution with a fixed rate.
   - Can be cancelled using the `cancel()` method.
   - If a task takes longer than the period, the next execution will wait for the current one to complete.

```java
ScheduledFuture<?> future = scheduler.scheduleAtFixedRate(() -> System.out.println("Fixed rate task"), 0, 5, TimeUnit.SECONDS);
```

4. `scheduleWithFixedDelay(Runnable command, long initialDelay, long delay, TimeUnit unit)`
   - Similar to `scheduleAtFixedRate`, but the delay is between the end of one execution and the start of the next.

```java
ScheduledFuture<?> future = scheduler.scheduleWithFixedDelay(() -> System.out.println("Fixed delay task"), 0, 5, TimeUnit.SECONDS);
```

## ThreadLocal

ThreadLocal class provides thread-local variables.

- Each thread has its own copy of the thread-local variable.
- Only one object of ThreadLocal class is needed, and each thread can use it to set and get its own thread-local variable.

```java
ThreadLocal<Integer> threadLocal = new ThreadLocal<>();

// In Thread 1
threadLocal.set(1);
Integer value1 = threadLocal.get(); // Returns 1

// In Thread 2
threadLocal.set(2);
Integer value2 = threadLocal.get(); // Returns 2
```

## Virtual Threads vs Platform Threads

### Platform Threads
- Also called normal threads.
- When created, JVM tells the OS to create a native/OS thread.
- One-to-one mapping with OS threads.
- Managed by JVM, but ultimately created by the OS.
- Disadvantages:
  - Slow creation process (system call).
  - Resource-intensive when waiting (e.g., for I/O operations).

### Virtual Threads
- Introduced to tackle the disadvantages of platform threads.
- Managed by JVM, not directly mapped to OS threads.
- Multiple virtual threads can be multiplexed onto fewer OS threads.
- Advantages:
  - Faster creation and lower resource usage.
  - Better handling of blocking operations (e.g., I/O waiting).
- Main goal: Improve throughput, not necessarily latency.

```java
// Creating a virtual thread
Thread vThread = Thread.ofVirtual().start(() -> System.out.println("Hello from virtual thread"));

// Or using Thread.startVirtualThread()
Thread.startVirtualThread(() -> System.out.println("Another virtual thread"));
```

Virtual threads are particularly useful for I/O-bound applications where many threads spend time waiting. They allow for a higher number of concurrent operations without the overhead of creating numerous OS threads.

## Important Interview Questions

1. **Q: What is the difference between `shutdown()` and `shutdownNow()` methods in ExecutorService?**
   A: `shutdown()` initiates an orderly shutdown where previously submitted tasks are executed, but no new tasks are accepted. `shutdownNow()` attempts to stop all actively executing tasks, halts the processing of waiting tasks, and returns a list of the tasks that were awaiting execution.

2. **Q: How does `awaitTermination()` work, and when would you use it?**
   A: `awaitTermination()` blocks until all tasks have completed execution after a shutdown request, or the timeout occurs, or the current thread is interrupted, whichever happens first. It's used when you want to wait for the ExecutorService to complete all tasks after calling `shutdown()`.

3. **Q: What is the difference between `scheduleAtFixedRate()` and `scheduleWithFixedDelay()`?**
   A: `scheduleAtFixedRate()` attempts to execute the task at a fixed rate, regardless of how long each task takes. `scheduleWithFixedDelay()` waits for the specified delay between the end of one execution and the start of the next.

4. **Q: How does ThreadLocal work, and what are its use cases?**
   A: ThreadLocal provides thread-local variables, where each thread has its own, independently initialized copy of the variable. It's useful for maintaining thread-safe data that differs per thread, such as transaction IDs or user authentication information in web applications.

5. **Q: What are the main differences between virtual threads and platform threads in Java?**
   A: Platform threads are mapped one-to-one with OS threads and are more resource-intensive. Virtual threads are lightweight, managed by the JVM, and can be multiplexed onto a smaller number of OS threads. Virtual threads are designed to improve throughput for I/O-bound applications.

6. **Q: How do you create a virtual thread in Java?**
   A: Virtual threads can be created using `Thread.ofVirtual().start(Runnable)` or `Thread.startVirtualThread(Runnable)`.

7. **Q: What is the main advantage of using ScheduledThreadPoolExecutor over Timer?**
   A: ScheduledThreadPoolExecutor is more flexible, allows multiple threads, and provides better error handling. It also supports both fixed-rate and fixed-delay task scheduling.

8. **Q: How does the ExecutorService help in managing thread pools?**
   A: ExecutorService provides a higher-level replacement for managing thread creation. It separates task submission from task execution, allowing for better resource management and performance in concurrent applications.

9. **Q: What happens if an exception occurs in a scheduled task in ScheduledThreadPoolExecutor?**
   A: If an exception occurs, the task is terminated, but the executor continues to run. Future executions of the task are suppressed. To handle exceptions, you should wrap the task's code in a try-catch block.

10. **Q: How do virtual threads improve application performance?**
    A: Virtual threads improve performance by allowing a large number of concurrent operations without the overhead of creating numerous OS threads. This is particularly beneficial for I/O-bound applications where threads spend a lot of time waiting.
