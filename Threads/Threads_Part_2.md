
# Java Threading Basics

This README provides an overview of Java threading concepts, including thread creation, lifecycle, synchronization, and inter-thread communication.

## Table of Contents
1. [Creating Threads](#creating-threads)
2. [Thread Lifecycle](#thread-lifecycle)
3. [Synchronization and Thread Safety](#synchronization-and-thread-safety)
4. [Inter-Thread Communication](#inter-thread-communication)

## Creating Threads

### What?
In Java, there are two primary ways to create threads:
1. Implementing the `Runnable` interface
2. Extending the `Thread` class
   

### Why?
Threads allow concurrent execution of code, enabling better performance and responsiveness in applications.

### Where?
Threads are used in scenarios requiring parallel processing, such as handling multiple client requests in a server application or performing background tasks in a GUI application.

### When?
Use threads when you need to perform tasks concurrently or when you want to improve the overall responsiveness of your application.

### How?

#### 1. Implementing the `Runnable` Interface
![Screenshot 2024-07-13 135028](https://github.com/user-attachments/assets/b9f1fd5d-b6c0-4c91-ad65-b4527c7ba82c)

### Create a Runnable Object:

- Create a class that implements the Runnable interface.
- Implement the run() method to define the task for the thread.

### Start the Thread:

- Create an instance of the class that implements Runnable.
- Pass the runnable object to the Thread constructor.
- Start the thread using thread.start(), which internally calls the run method.

```java
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        // Task to be executed in a separate thread
        System.out.println("Thread is running");
    }
}

// Usage
Runnable runnable = new MyRunnable();
Thread thread = new Thread(runnable);
thread.start();
```

#### 2. Extending the `Thread` Class
![image](https://github.com/user-attachments/assets/d5e62807-d2e9-438e-a9b1-268977590129)

### Create a Thread Subclass:

- Create a class that extends the Thread class.
- Override the run() method to define the task for the thread.

### Start the Thread:

- Create an instance of the subclass.
- Call the start() method.
  
```java
public class MyThread extends Thread {
    @Override
    public void run() {
        // Task to be executed in a separate thread
        System.out.println("Thread is running");
    }
}

// Usage
MyThread thread = new MyThread();
thread.start();
```

### Why Choose One Over the Other?
- Implementing `Runnable` is preferred in industry standards because:
  - It allows a class to implement multiple interfaces
  - It promotes better separation of concerns
- Extending `Thread` is limited because Java doesn't support multiple inheritance of classes

## Thread Lifecycle
![image](https://github.com/user-attachments/assets/55bbff66-e876-4d62-9306-6323f90737f9)

### What?
The thread lifecycle consists of various states that a thread goes through during its execution.

### Why?
Understanding the thread lifecycle is crucial for effective thread management and debugging concurrent applications.

### Where?
The thread lifecycle is managed by the Java Virtual Machine (JVM).

### When?
Threads transition between states based on various factors such as method calls, resource availability, and JVM decisions.

### States:

1. **New**
   - Thread object is created but not started
   - It is just an object in the memory
   - `Thread t = new Thread();`

2. **Runnable**
   - Thread is ready to run, waiting for CPU time
   - After calling `t.start();`

3. **Running**
   - Thread is currently executing
   - JVM allocates CPU time to the thread

4. **Blocked**
   - Thread is temporarily inactive
   - I/O - like reading from a file or database.
   - Lock Aquired - If the thread want to lock on a resource which is locked by other thread,it has to wait.
   - Releases all the monitor locks.
   - Waiting for a monitor lock or I/O operations
   - Example: Waiting to acquire a lock on a synchronized block

5. **Waiting**
   - Thread is waiting indefinitely for another thread to perform a particular action
   - Thread goes into this state when wait() is called.
   - It goes back to the runnable state once notify() or notifyAll() is called.
   - Releases all MONITOR LOCKS.
   - Entered by calling `Object.wait()`, `Thread.join()`, or `LockSupport.park()`

6. **Timed Waiting**
   - Thread is waiting for a specified period
   - Returns to the runnable state after the specified condition is met.
   - Does not release any MONITOR LOCKS.
   - Entered by calling `Thread.sleep()`, `Object.wait(timeout)`, `Thread.join(timeout)`, etc.

7. **Terminated**
   - Thread has completed execution or was terminated abnormally

```java
public class ThreadLifecycleDemo {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            try {
                Thread.sleep(1000); // Timed Waiting state
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });

        System.out.println("New State: " + t.getState()); // NEW

        t.start();
        System.out.println("After start(): " + t.getState()); // RUNNABLE

        Thread.sleep(100);
        System.out.println("Sleeping: " + t.getState()); // TIMED_WAITING

        t.join();
        System.out.println("Terminated: " + t.getState()); // TERMINATED
    }
}
```

## Synchronization and Thread Safety

### What?
Synchronization is a mechanism to control access to shared resources in a multi-threaded environment.

### Why?
To prevent race conditions and ensure data consistency when multiple threads access shared resources.

### Where?
Used in scenarios where multiple threads might simultaneously access or modify shared data.

### When?
Implement synchronization when you have critical sections in your code that should be executed by only one thread at a time.

### How?

#### Synchronized Methods

```java
public class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public synchronized int getCount() {
        return count;
    }
}
```

#### Synchronized Blocks

```java
public class Counter {
    private int count = 0;
    private final Object lock = new Object();

    public void increment() {
        synchronized(lock) {
            count++;
        }
    }

    public int getCount() {
        synchronized(lock) {
            return count;
        }
    }
}
```

## Inter-Thread Communication

### What?
Mechanisms for threads to communicate and coordinate their actions.

### Why?
To allow threads to work together efficiently and avoid busy-waiting.

### Where?
Used in producer-consumer scenarios, task coordination, and other situations where threads need to signal each other.

### When?
Implement inter-thread communication when you need threads to wait for certain conditions or notify other threads about changes in shared state.

### How?

#### wait(), notify(), and notifyAll() methods
- Java provides methods like wait(), notify(), and notifyAll() for inter-thread communication.
  
- wait(): Causes the current thread to wait until another thread invokes notify() or notifyAll() on the same object.
- notify(): Wakes up a single thread that is waiting on the object's monitor.
- notifyAll(): Wakes up all threads that are waiting on the object's monitor.

```java
public class SharedResource {
    private boolean isDataReady = false;

    public synchronized void produceData() {
        isDataReady = true;
        notify(); // Notify waiting consumer
    }

    public synchronized void consumeData() throws InterruptedException {
        while (!isDataReady) {
            wait(); // Wait until data is ready
        }
        System.out.println("Data consumed");
        isDataReady = false;
    }
}
```
