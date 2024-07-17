

## Future and callable in java

- we wanted to know the status of the thread in async mode by some other thread
- now what if caller want to know the status of thread1. whether its completed or failed etc.

### Future
- It is an interface whihc respresents the result of the async task
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
    
- Future task will have two states runnable, state , output
- submit(runnable)
- submit(runnable,t)
- submit(callable<>)
- implement the above three submit use cases in java
- Why the question mark in Future<?>?
  - ? - wildcard it can be anything
### Callable
- Callable represents the task whihc need to be executed just like Runnable
- But difference is :
  - Runnable does not have any return type
  - Whereas callabe have return type and it can return value.

### CompletableFuture:
- Introduced in java8
- To help in async prograamming
- We can considered it as an advanced version of future provides additional capability like chaining

### About sync and async differenes as well (notes)

### how to use this:(from notes)
1. CompletableFuture.supplyAsync:
   - supplyAsync method initates an async operation
2. thenApply and thennApplyAsync
3. thenCompose and thenComposeAsync
4. thenAccept and thenAcceptAsync
5. thenCombine and thenCombineAsync
