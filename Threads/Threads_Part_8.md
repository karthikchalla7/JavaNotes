## Executors Utility and Fork Join Pool

### Introduction to Thread Pool Executors

Thread Pool Executors are a powerful way to manage and optimize thread usage in Java applications. They are part of the `java.util.concurrent` package and provide a high-level API for executing tasks asynchronously.

Custome ThreadPoolExecutor
```java
ThreadPoolExecutor executor = new ThreadPoolExecutor(1,1,10, TimeUnit.MINUTES,
                new ArrayBlockingQueue<>(2), Executors.defaultThreadFactory(),new ThreadPoolExecutor.AbortPolicy());

```

### Types of Thread Pool Executors
- Executors provides Factory methods which we can use to create ThreadPoolExecutor
  
### 1. Fixed ThreadPoolExecutor

The Fixed ThreadPoolExecutor creates a thread pool with a fixed number of threads.

#### Key Points:
- Created using `Executors.newFixedThreadPool(nThreads)` method
- The minimum and maximum pool size is the same
- If all threads are active when a new task is submitted, it will be queued until a thread becomes available
![image](https://github.com/user-attachments/assets/5e37b7e8-216a-4392-91e4-5a721757a99c)

#### Example:

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class FixedThreadPoolExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(5);
        
        for (int i = 0; i < 10; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("Task " + taskId + " is running on thread " + Thread.currentThread().getName());
            });
        }
        
        executor.shutdown();
    }
}
```

This example creates a fixed thread pool with 5 threads and submits 10 tasks. The tasks will be executed by the 5 threads in the pool.

### 2. Cached ThreadPoolExecutor

The Cached ThreadPoolExecutor creates new threads as needed and reuses previously constructed threads when they are available.

#### Key Points:
- Created using `Executors.newCachedThreadPool()` method
- Threads that have been idle for 60 seconds are terminated and removed from the cache
- The pool size can grow unbounded
- Queue size is zero because it creates threads as soon as a task comes
![image](https://github.com/user-attachments/assets/aa522e2c-3e31-4681-a159-8c86b6f9a9f5)

#### Example:

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class CachedThreadPoolExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newCachedThreadPool();
        
        for (int i = 0; i < 100; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("Task " + taskId + " is running on thread " + Thread.currentThread().getName());
            });
        }
        
        executor.shutdown();
    }
}  
```

This example creates a cached thread pool and submits 100 tasks. The pool will create new threads as needed and reuse them when available.

### 3. Single Thread Executor

The Single Thread Executor uses a single worker thread to execute tasks.

#### Key Points:
- Created using `Executors.newSingleThreadExecutor()` method
- Guarantees that tasks are executed sequentially
- Useful for scenarios where tasks must be executed in a specific order
![image](https://github.com/user-attachments/assets/d3aab544-1679-40ed-899e-a88cb954ac18)

#### Example:

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class SingleThreadExecutorExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newSingleThreadExecutor();
        
        for (int i = 0; i < 5; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("Task " + taskId + " is running on thread " + Thread.currentThread().getName());
            });
        }
        
        executor.shutdown();
    }
}
```

This example creates a single thread executor and submits 5 tasks. All tasks will be executed sequentially by a single thread.

### 4. WorkStealing Pool Executor

The WorkStealing Pool Executor is based on the Fork-Join framework and is designed for maximum parallelism.

#### Key Points:
- Created using `Executors.newWorkStealingPool()` method
- Uses the Fork-Join pool internally
- Allows for efficient distribution of tasks among multiple threads

#### Understanding Fork-Join Pool:

The Fork-Join framework is designed to help speed up parallel processing by attempting to use all available processor cores. It follows a divide-and-conquer approach:

1. Fork: Split a large task into smaller subtasks that can be processed in parallel.
2. Join: Wait for all subtasks to finish and combine their results.

#### Work Stealing Concept:

- Each thread in the pool has its own deque (double-ended queue) known as a work-stealing queue.
- There's also a shared submission queue for incoming tasks.
- When a thread is idle, it follows this priority:
  1. Check its own work-stealing queue
  2. Check the submission queue
  3. Try to "steal" work from other threads' work stealing queues
- Task can be split into multiple small sub-tasks. For that Task should extend:
  - RecursiveTask
  - RecursiveAction
- We can create Fork-Join Pool using "newWorkStealingPool" method in ExecutorService.
- Or by calling ForkJoinPool.commonPool() method.
- 
#### Example 1:

```java
import java.util.concurrent.RecursiveTask;

public class RecursiveComputeSumTask extends RecursiveTask<Integer> {

    int start;
    int end;
    RecursiveComputeSumTask(int start,int end){
        this.start = start;
        this.end = end;
    }

    @Override
    protected Integer compute() {

        //this is the base condition
        if(end-start<=4){
            int totalSum = 0;
            for (int i=start;i<=end;i++){
                totalSum+=i;
            }
            return totalSum;
        }

        else{
            int mid = (start+end)/2;
            RecursiveComputeSumTask leftTask = new RecursiveComputeSumTask(start,mid);
            RecursiveComputeSumTask rightTask = new RecursiveComputeSumTask(mid+1,end);

            //Fork the subtasks for parallel execution
            leftTask.fork();
            rightTask.fork();

            //combine the results of subtasks
            int leftRes = leftTask.join();
            int rightRes = rightTask.join();

            //return result
            return leftRes+rightRes;
        }

    }
}

public class Main{
public static void main(String[] args) {
        System.out.println("The number of processors: "+Runtime.getRuntime().availableProcessors());

        // we can also use workStealing threadpool
        ForkJoinPool pool = new ForkJoinPool();
        Future<Integer> futureObj = pool.submit(new RecursiveComputeSumTask(0,100));
        try {
            System.out.println(futureObj.get());
        }catch (Exception e){

        }
    }
}
```

#### Example 2:
```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

public class WorkStealingPoolExample {
    static class FibonacciTask extends RecursiveTask<Integer> {
        final int n;

        FibonacciTask(int n) { this.n = n; }

        @Override
        protected Integer compute() {
            if (n <= 1) return n;
            FibonacciTask f1 = new FibonacciTask(n - 1);
            f1.fork();
            FibonacciTask f2 = new FibonacciTask(n - 2);
            return f2.compute() + f1.join();
        }
    }

    public static void main(String[] args) {
        ExecutorService executor = Executors.newWorkStealingPool();
        
        if (executor instanceof ForkJoinPool) {
            ForkJoinPool forkJoinPool = (ForkJoinPool) executor;
            FibonacciTask task = new FibonacciTask(20);
            int result = forkJoinPool.invoke(task);
            System.out.println("Fibonacci(20) = " + result);
        }
        
        executor.shutdown();
    }
}
```

This example demonstrates a work-stealing pool used to calculate Fibonacci numbers recursively. The `FibonacciTask` extends `RecursiveTask` and uses the fork-join mechanism to parallelize the computation.

## Addressing Your Questions

1. How to divide the task into subtasks?
   - In the Fork-Join framework, you typically divide tasks by creating new instances of your custom `RecursiveTask` or `RecursiveAction` classes. The `compute()` method is where you implement the logic to split the task.

2. How to put the task into the work-stealing queue?
   - Use the `fork()` method on a task to schedule it for asynchronous execution. This puts the task into the work-stealing queue.

3. When should you use RecursiveTask vs RecursiveAction?
   - Use `RecursiveTask` when your subtask needs to return a value.
   - Use `RecursiveAction` when your subtask doesn't need to return a value (void).

4. How many threads can we create in a fork-join pool?
   - By default, the number of threads in a fork-join pool is equal to the number of available processors (Runtime.getRuntime().availableProcessors()). You can specify a different number when creating the pool.

5. Why use a deque for the work-stealing queue?
   - Deques (double-ended queues) are used because they allow efficient operations at both ends. The owning thread pushes and pops tasks from one end, while "thief" threads steal tasks from the other end, minimizing contention.
