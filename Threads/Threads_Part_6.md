
## ThreadPool and Executor Framework

This README provides an overview of ThreadPool and the Executor Framework in Java, focusing on the ThreadPoolExecutor class.

### Table of Contents

1. [ThreadPool](#threadpool)
2. [Executor Framework](#executor-framework)
3. [ThreadPoolExecutor](#threadpoolexecutor)
4. [Interview Question](#interview-question)

### ThreadPool

### Definition
- A collection of threads (workers) available to perform submitted tasks
- Worker threads return to the pool after task completion, waiting for new assignments
- Allows thread reuse

### Advantages
- Thread creation time can be saved.
  - when each thread created, space is allocated to it(stack,heap,program counter etc) and this takes time.
  - with threadpool this can be avoided by resuing thread.
- Overhead of managing the thread lifecycle can be removed:
  - read about thread lifecycle.
  - Thread has different state like running,waiting,terminate etc.And managing thread state included complexity
  - Thread pool abstract away this management of threads
- Increased the performace:
  - More threads means, more context switching time,using threadpool control over thread creation, excess context switching can be avoided.
    - more processing time less context switiching.

### How it works
- Tasks are kept in a queue
- Available threads take tasks from the queue
- If no threads are available, tasks are stored in the queue until a thread becomes free

### Executor Framework (java.util.concurrent)
![image](https://github.com/user-attachments/assets/e7291262-1b83-471e-bb8a-42b75ad8972d)


### ThreadPoolExecutor (Very Important)

### Purpose
- Creates a customizable thread pool

### Workflow Example
1. Minimum thread pool (e.g., 3 threads)
2. Tasks assigned to available threads
3. When no threads are free, tasks are queued
4. If queue is full and max threads not reached, new threads are created
5. If everything is full (queue and max threads), tasks are rejected
6. If any of the threads are free then the tasks from the queue will be executed by the thread

### Why are using queue and storing the tasks when we have access to create maxPoolsize (max threads) process it?
- It is because on and avg the minthreadsize is sufficient to run the tasks and also which are in the queue
- Lets say we have created thread using maxpoolsize and completed the tasks wihtout using queue.
- now once the task are done those thread will comeback and stay in pool and stay idle because most of the tasks can be completed using minpoolszie threads


### Constructor
```java
public ThreadPoolExecutor(
    int corePoolSize,
    int maximumPoolSize,
    long keepAliveTime,
    TimeUnit unit,
    BlockingQueue<Runnable> workQueue,
    ThreadFactory threadFactory,
    RejectedExecutionHandler handler
)
```

### Parameters Explained
- corePoolSize - Number of threads are initially created and kept in the pool, even if they are idle.
- allowCoreThreadTimeout - If this property is set to TRUE(default is FALSE),idle thread kept Alive till time specified by "KeepAliveTime"
- KeepAliveThread - Thread which are idle get terminated after this time.
- TimeUnit - TimeUnit for the keepAliveTime, whether Millisecond or Second or Hours etc
- maximumPoolSize - Maximum number of thread in a pool
  - if no of thread are == corePoolsize and queue is also full, then new threads are created(till its less than maxPoolSize)
  - Excess thread, will remain in pool this pool is not shutdown or if allowCoreThreadTimeout is set to true,
  - then excess thread get terminated after reamin idle for keepAliveTime.
- BlockingQueue<Runnable> Queue
  - Queue used to hold tasks before they got picked by the worker thread
  - Bounded queue : Queue with FIXED capacity - Like : ArrayBlockingQueue
  - Unbounded queue : Queue with NO FIXED capacity - like : LinkedBlockingQueue
  - Generally Bounded queue is prefered mostly which can be full control.
- ThreadFactory:
  - Factory for creating new thread.
  - ThreadPoolExectuor use this to create new thread, this factory provide us an interface to
  - To give custom thread name, thread priority, Daemon Falg etc.
- RejectedExcutionHandler
  
   ![image](https://github.com/user-attachments/assets/77922270-6a66-4390-a48e-140e3b2ab289)


### Lifecycle of ThreadPoolExecutor
![image](https://github.com/user-attachments/assets/a24f8943-f576-4725-9d3e-4bf3f6d60195)

- Running:
  - Executor is in running state and submit() method will be used to add new task
- Shutdonw:
  - Executor do not accept new tasks but continue to process existing tasks once existing tasks finished, executor moves to terminate state
  - Method used shutdown()
- Stop(force shutdown)
  - Executor do not accept new tasks
  - Executor forefully stops all the tasks which are currently executing
  - And once fully shutdown, moves to terminate state
  - Method used shutdownNow()
- Terminated:
  - End of life for particular ThreadPoolExecutor
  - isTerminated() method can be used to check if particular thread pool executor is terminated or not.

### Example:

```java
package Threads_Part_6;
import java.util.concurrent.*;

public class Main{

public static void main(String[] args) {

        ThreadPoolExecutor executor = new ThreadPoolExecutor(2,4,10, TimeUnit.MINUTES,
                new ArrayBlockingQueue<>(2),new CustomThreadFactory(),new CustomRejectHandler());
        //after certain if any threads are idle it will be eliminated
        executor.allowCoreThreadTimeOut(true);
        //change the number of task to understand clearly how threadpool is working.
        for (int i = 1; i <=8 ;i++) {
            executor.submit(()->{
                try{
                    Thread.sleep(5000);
                }catch (Exception e){
                    //handle exception
                }
                System.out.println("Task processed by "+Thread.currentThread().getName());
            });
        }
        executor.shutdown();
    }

}
class CustomThreadFactory implements ThreadFactory{

    @Override
    public Thread newThread(Runnable r) {
        Thread th =  new Thread(r);
        //default methods
//        th.setName("custom Thread");
        th.setPriority(Thread.NORM_PRIORITY);
        th.setDaemon(false);
        return  th;
    }
}

class CustomRejectHandler implements RejectedExecutionHandler{

    @Override
    public void rejectedExecution(Runnable r, ThreadPoolExecutor executor) {

        System.out.println("Task Rejected: "+r.toString());
    }
}
```
### Important Interview Question
Q: In a thread pool, why choose a specific corePoolSize (e.g., 2 instead of 10 or 15)?
A: The choice of corePoolSize depends on various factors:
- CPU Cores
- JVM Memory (talk about the heap and each thread will have(stack,counter,..))
  - Even if we have powerful cores memory is very important to create threads
  - so it depends on memory as well
- Task Natue (CPU intensive or I/O intensive)
  - less thread in CPU intensive
  - more threads in I/O intensive since threads are idle in this state
- Concurrency Requirement (Want high or medium or low concurrency)
- Memory Required to process a request (check if heap can support or no)
- Throughput etc.(how fast we can process the request)

And its an interative process to update the min and max values based on monitoring.  
Formula to find the no.of threads:  
Max no of thread = No. of CPU cores *(1 + Request Waiting time /Processing time)  
The above formula focusing on CPU CORE,TASK NATURE and THROUGHPUT.  
It is good to have as many threads as cpu cores.  

But the above formula does not consider memory yet, whihc need to be consider
```
Lets say
JVM: 2GB  
  Heap Space: 1 Gb
  Code Cache space : 128mb
  per thread space : 5mb * no.of threads(include thread stack space)
  JVM overhead : 256mb
```
Now we are left with 500mb to create threads.  
Lets say each thread consumes 5mb so with the memory above we can create 100 threads.  
Now we have to consider memory required to process the request  
Per request requires 10mb space to fulfill the request and this is stored in heap memory  
This overload the heap memory so we can create 60% to create threads  
```
min = 60
max = 64
Here we need to do iterative monitorting and perfrom load testing and optimize the number
```
