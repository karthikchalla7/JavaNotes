
## Threads

This README provides an overview of including deprecated methods, thread joining, thread priority, and daemon threads.

## Deprecated Methods: stop(), suspend(), resume()

### Why are they deprecated?

These methods were deprecated due to their potential to cause deadlocks and other synchronization issues.

#### stop()
- Terminates the thread abruptly
- Does not release locks
- No resource cleanup occurs

#### suspend()
- Temporarily puts the thread on hold
- Does not release locks

#### resume()
- Used to resume execution of a suspended thread

### Issues with these methods:
- Can lead to deadlock situations due to not releasing locks
- Abrupt termination can leave shared data in an inconsistent state

## Thread Joining

The `join()` method is used for thread synchronization and coordination.
```java
public class ThreadJoinExample {
    public static void main(String[] args) {
        Thread thread = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                System.out.println("Child Thread: " + i);
                try {
                    Thread.sleep(500);
                } catch (Exception e) {
                    //catch expression goes here
                }
            }
        });

        thread.start();
        try {
            thread.join(); // Main thread waits for thread to finish
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("Main Thread: Finished");
    }
}
```
### Key points:
- When `join()` is invoked on a thread object, the current thread blocks and waits for the specified thread to finish
- Useful for coordinating between threads or ensuring certain tasks complete before moving ahead
- Example: If thread T1 calls `T2.join()`, T1 will wait until T2 completes before continuing

## Thread Priority

Thread priority is a hint to the thread scheduler about the order of thread execution.

### Key points:
- Priority ranges from 1 (lowest) to 10 (highest)
- Default priority is 5 (NORM_PRIORITY)
- Set using `setPriority(int priority)` method
- A new thread inherits the priority of its parent thread
- Thread scheduler is not guaranteed to follow priority order strictly
- Not reliable for production use

### Constants for priority:
- `Thread.MIN_PRIORITY` (1)
- `Thread.NORM_PRIORITY` (5)
- `Thread.MAX_PRIORITY` (10)

## Daemon Threads

Daemon threads are background threads that provide services to user threads.

### Key characteristics:
- Run asynchronously
- Automatically terminate when all user threads complete
- Created using `setDaemon(boolean)` method
- Useful for background tasks like garbage collection or auto-saving

### Examples of daemon threads:
- Garbage collector
- Auto-save feature in text editors
- Logging services

### Important notes:
- JVM will exit when all user threads have completed, even if daemon threads are still running
- Daemon threads should not be used for critical tasks or those requiring orderly shutdown

## Best Practices

1. Avoid using deprecated methods (stop, suspend, resume)
2. Use `join()` for thread coordination when necessary
3. Be cautious with thread priorities; don't rely on them for critical timing
4. Use daemon threads for background tasks that can terminate abruptly

Remember, thread behavior can vary between different JVM implementations and operating systems. Always test thoroughly and avoid relying on specific thread scheduling behaviors.
