

## Future and callable, CompletableFuture in java

### Tables of Content:
1. [Future](#Future)
2. [Callable](#Callable)
3. [Use Cases of submit](#Use-cases-of-submit-method)
4. [CompletableFuture](#CompletableFuture)
5. [About Sync and Async](#About-sync-and-async)
6. [How to use CompletableFuture](#How-to-use-CompletableFuture)

- we wanted to know the status of the thread in async mode by some other thread
- now what if caller want to know the status of thread1. whether its completed or failed etc.

### Future
- It is an interface which respresents the result of the async task
- mean it allow you to check if:
  - computation is complete
  - get the result
  - take care of exception if any
- Whenever we submit a task to threadpoolexecutor will return futureobj
- Inside future we have five methods
  1. boolean cancel(boolean mayInterruptRunning)
     - Attempts to cancel the execution of the task
     - Returns false, if task can not be cancelled(typically becaouse task already completed return true other wise
  2. boolean isCancelled() - returns true if the task was cancelled before it gets completed
  3. boolean isDone()
     - Returns true if the task completed
     - completion may be due to noram termination, an exception or cancellation - in all of these cases, this method will return true
  4. V get()
     - Wait if required,for the completion of the task
     - after task completed retrieve the result if avialable
  5. V get(long timeout, TimeUnit unit)
     - Wait if required for at most the given timeout period
     - Throws 'TimeoutException' if timeout period finished and task is not yet completed

```java
public static void main(String[] args) {

        ThreadPoolExecutor executor = new ThreadPoolExecutor(1,1,10, TimeUnit.MINUTES,
                new ArrayBlockingQueue<>(2), Executors.defaultThreadFactory(),new ThreadPoolExecutor.AbortPolicy());

        Future<?> futureobj = executor.submit(()->{
           try{
               Thread.sleep(7000);
               System.out.println("This is the task which thread will execute");
           }catch (Exception e){

           }

        });
        System.out.println("Is done: "+futureobj.isDone());

        try {
            futureobj.get(2,TimeUnit.SECONDS);
        }catch (TimeoutException | InterruptedException | ExecutionException e){
            System.out.println("TimeoutException happend");
        }

        //waiting period this step does
        //it will wait till thread completes
        //when we do get it will execute the thread
        try {
            futureobj.get();
        }catch (Exception e){

        }
        System.out.println("is done "+futureobj.isDone());
        System.out.println("is cancelled: "+futureobj.isCancelled());
  ```
### Why `Future<?>`?
The `?` is a wildcard in Java generics. It means the Future can return any type, allowing for flexibility when the return type is unknown or irrelevant.

### Callable
- Callable represents the task whihc need to be executed just like Runnable
- But difference is :
  - Runnable does not have any return type
  - Whereas callabe have return type and it can return value.

```java
//Runnable Interface
@FunctionalInterface
public interface Runnable{
public abstract void run();
}

//Callable Interface
@FunctionalInterface
public interface Callabe<V>{
V call() throws Exception;
}
```

### Use cases of Submit method
1. submit(runnable)

```java
  public static void main(String[] args) {

        //Create a thread pool executor
        ThreadPoolExecutor poolExecutor = new ThreadPoolExecutor(3,3,1, TimeUnit.HOURS,
                new ArrayBlockingQueue<>(10), Executors.defaultThreadFactory(),new ThreadPoolExecutor.AbortPolicy());

        //use case 1
        Future<?> futureobj1 = poolExecutor.submit(()->{
            System.out.println("Task1 with runnable");
        });

        //.get ensures to comoplete the thread
        try{
            Object obj = futureobj1.get();
            System.out.println(obj == null);
        }catch (Exception e){
            
        }
}
```

2. submit(runnable,t)
```java
//use case 2

        List<Integer> output = new ArrayList<>();
        Future<List<Integer>> futureobj2 = poolExecutor.submit(()->{

            output.add(100);
            System.out.println("Task2 with Runnable and Return object");
        },output);
      try{
            List<Integer> outputfromFutureObj2 = futureobj2.get();
            System.out.println(outputfromFutureObj2.get(0));
        }catch (Exception e){

        }

```
3. submit(callable<>)

```java
//use case 3
        Future<List<Integer>> futureobj3 = poolExecutor.submit(()->{
            System.out.println("Task3 with callable");
            List<Integer> listojb = new ArrayList<>();
            listojb.add(200);
            return listojb;
        });

        try{
            List<Integer> outputFromFuturObj3 = futureobj3.get();
            System.out.println(outputFromFuturObj3.get(0));
        }catch (Exception e){

```

### CompletableFuture:
- Introduced in java8
- To help in async prograamming
- We can considered it as an advanced version of future provides additional capability like chaining
  
### Key Features
- Combines the functionality of Future with additional methods for composing, combining, and handling asynchronous computations
- Supports functional programming style with lambda expressions
- Allows for better exception handling in asynchronous code

### About sync and async 

![image](https://github.com/user-attachments/assets/9316794a-434b-4d41-b01f-126255897990)

- Examples in Real-world Scenarios:

- Synchronous: Reading and writing files in a single thread, simple sequential tasks.
- Asynchronous: Handling network requests, updating UI elements in response to user interactions, parallel processing of large data sets.

#### Synchronous Programming 
```java
//1.simple method call
void perfromTasks(){
task1();
task2();
task3();
}

//In this example, task2 will not start until task1 is completed, and task3 will wait for task2.

//2.Synchronized block in java:
synchronized void syncMethod() {
    // Critical section
}

//Only one thread can execute this synchronized method at a time, and other threads must wait.

```
#### Asynchronous Programming

```java
//1. Using 'CompletableFuture:

CompletableFuture.supplyAsync(() -> {
    // Simulate a long-running task
    return "Task Result";
}).thenAccept(result -> {
    // Process the result
    System.out.println(result);
});

//Here, supplyAsync runs the task in a separate thread, allowing the main thread to continue execution.

2. Using ExecutorService:

ExecutorService executor = Executors.newFixedThreadPool(5);
executor.submit(() -> {
    // Task to be executed asynchronously
    System.out.println("Task executed");
});
executor.shutdown();

//The task submitted to the executor is executed asynchronously, allowing the main thread to continue without waiting.

```

### How to use CompletableFuture
1. CompletableFuture.supplyAsync:
![image](https://github.com/user-attachments/assets/ca6c7f19-71d4-4170-8a98-c8d97feb3564)

- supplyAsync method initates an async operation
- 'supplier' is executed asynchronously in a seperate thread.
- If we want more control on threads, we can pass Executor in the method.
- By default it uses, shared Fork-Join Pool executor.
- It dynamically adjust its pool size based on processors.

```java
    public static void main(String[] args) {

        ThreadPoolExecutor poolExecutor = new ThreadPoolExecutor(1,1,1, TimeUnit.SECONDS,
                new ArrayBlockingQueue<>(10), Executors.defaultThreadFactory(), new ThreadPoolExecutor.AbortPolicy());

        //1. supplyAsync method - it creates one mor thread other than main thread.
        try {
            CompletableFuture<String> asynctask1 = CompletableFuture.supplyAsync(() -> {
                //this is the task which need to be completd by thread:
                return "task completed";
            }, poolExecutor);
            System.out.println(asynctask1.get());
        }catch (Exception e){

        }        
        //this helps to removes all threads from thread pool once everthing is done
        //if not the thread will be waiting for the specified time in the ThreadPoolExecutor
        poolExecutor.shutdown();

    }
```
2. thenApply and thennApplyAsync
- Apply a function to the result of previous Async operation
- Return a new CompletableFuture Object.

- thenApply method:
  - It is a synchronous execution
  - Means, it uses same thread which completed the previous Async task.

- thenApplyAsync method:
  - It is a Asynchronous execution
  - Means, it uses a different thread(from 'fork-join' pool, if we do not provide the execturo in the method), to complete this function.
  - If multiple 'thenApplyAsync' is used, ordering can not be guarantee they will run concurrently.

- Examples of both the methods:

```java
//2. thenApply method
        CompletableFuture<String> asyncapplytask1 = CompletableFuture.supplyAsync(()->{
            //task whihc thread need to execute;
            return  "Completable ";
        },poolExecutor).thenApply((String val)->{
            System.out.println("From thenApply");
            return  val + "Future";
        });
        try{
            System.out.println(asyncapplytask1.get());
        }catch (Exception e){

        }

        //3. thenApplyAsync() method
        CompletableFuture<String> applyasynctask = CompletableFuture.supplyAsync(()->{
            System.out.println("Thread name whihc run 'supplyAsync': "+Thread.currentThread().getName());
            return "LearningCompletable";
        },poolExecutor).thenApplyAsync((String val)->{
            System.out.println("Thread Name whihc runs 'thenapply':"+Thread.currentThread().getName());
            return val + "Future";
        });

        try {
            System.out.println(applyasynctask.get());
        }catch (Exception e){

        }
        System.out.println("Thread name for 'after cf': "+Thread.currentThread().getName());

```

3. thenCompose and thenComposeAsync
- Chain together dependent Async operations
- Means when next Async operation depends on the result of the previous Async one we can tie them together.
- For Async tasks, we can bring some ordering using this.

```java
//4.thenCompose and thenComposeAsync

        CompletableFuture<String> composetask = CompletableFuture.supplyAsync(()->{
            System.out.println("Thread Name whihc runs 'supplyAsync': "+Thread.currentThread().getName());
            return "This is from compose ";
        },poolExecutor).
                thenCompose((String val)->{
                    return CompletableFuture.supplyAsync(()->{
                        System.out.println("Thread name whihc runs 'thenCompose' "+Thread.currentThread().getName());
                        return val + "learning";
                    });
                });
        try{
            System.out.println(composetask.get());
        }catch (Exception e){

        }
```

4. thenAccept and thenAcceptAsync
- Generally end stage, in the chain of Async operations
- It does not return anything.

```java
//5.thenAccept and thenAcceptAsync

        CompletableFuture<Void> accepttask = CompletableFuture
                .supplyAsync(()->{
                    System.out.println("Thread name whihc runs 'supplyasync': "+Thread.currentThread().getName() );
                    return "This from accept and ";
                },poolExecutor)
                .thenAccept((String val)-> System.out.println("All stages Completed"));
        try {
            //it prints null because thenAccept does not return anything.
            System.out.println(accepttask.get());
        }catch (Exception e){

        }
```
5. thenCombine and thenCombineAsync
- Used to combine the result of 2 comparable future.

```java
//6.thenCombine and thenCombineAsync

        CompletableFuture<Integer> task1 = CompletableFuture.supplyAsync(()->{
            return 10;
        },poolExecutor);

        CompletableFuture<String> task2 = CompletableFuture.supplyAsync(()->{
            return "K";
        });

        CompletableFuture<String > combinedTasks = task1.thenCombine(task2,(Integer val1,String val2)->val1+val2);
        try {
            System.out.println("The combined value: "+combinedTasks.get());
        }catch (Exception e){
            
        }
```
