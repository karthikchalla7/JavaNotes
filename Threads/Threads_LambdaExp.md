
### Creating Threads with Runnable and Lambda Expressions

In Java, lambda expressions provide a concise way to implement functional interfaces, such as `Runnable`. Below are the steps to substitute the traditional implementation of the `Runnable` interface with a lambda expression.

### Traditional Runnable Implementation

1. **Create a Runnable Object**:
   - Create a class that implements the `Runnable` interface.
   - Implement the `run()` method to define the task for the thread.

2. **Start the Thread**:
   - Create an instance of the class that implements `Runnable`.
   - Pass the runnable object to the `Thread` constructor.
   - Start the thread using `thread.start()`.

```java
class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Thread is running.");
    }
}

public class TestRunnable {
    public static void main(String[] args) {
        MyRunnable runnable = new MyRunnable();
        Thread thread = new Thread(runnable);
        thread.start();
    }
}
```

### Using Lambda Expression for Runnable

Since `Runnable` is a functional interface with a single abstract method `run()`, it can be replaced with a lambda expression.

#### Lambda Expression with Runnable

1. **Create a Lambda Runnable Object**:
   - Use a lambda expression to define the `run()` method.

2. **Start the Thread**:
   - Pass the lambda expression directly to the `Thread` constructor.
   - Start the thread using `thread.start()`.

```java
public class TestRunnableWithLambda {
    public static void main(String[] args) {
        Runnable runnable = () -> {
            System.out.println("Thread is running.");
        };
        Thread thread = new Thread(runnable);
        thread.start();
    }
}
```

#### Simplified Lambda Expression

For even more concise code, you can pass the lambda expression directly to the `Thread` constructor without assigning it to a variable.

```java
public class TestRunnableWithLambda {
    public static void main(String[] args) {
        Thread thread = new Thread(() -> {
            System.out.println("Thread is running.");
        });
        thread.start();
    }
}
```

### Example

Here’s an example where the `Runnable` performs a more complex task, such as printing numbers from 1 to 5.

#### Traditional Runnable Implementation

```java
class MyRunnable implements Runnable {
    public void run() {
        for (int i = 1; i <= 5; i++) {
            System.out.println(i);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                System.out.println(e);
            }
        }
    }
}

public class TestRunnable {
    public static void main(String[] args) {
        MyRunnable runnable = new MyRunnable();
        Thread thread = new Thread(runnable);
        thread.start();
    }
}
```

#### Runnable with Lambda Expression

```java
public class TestRunnableWithLambda {
    public static void main(String[] args) {
        Runnable runnable = () -> {
            for (int i = 1; i <= 5; i++) {
                System.out.println(i);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    System.out.println(e);
                }
            }
        };
        Thread thread = new Thread(runnable);
        thread.start();
    }
}
```

#### Simplified Runnable with Lambda Expression

```java
public class TestRunnableWithLambda {
    public static void main(String[] args) {
        Thread thread = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                System.out.println(i);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    System.out.println(e);
                }
            }
        });
        thread.start();
    }
}
```

### Summary

Using lambda expressions with the `Runnable` interface simplifies your code by eliminating boilerplate code for anonymous classes or separate class files. It makes the code more readable and concise, especially for short tasks.

**Traditional Implementation:**

```java
Runnable runnable = new MyRunnable();
Thread thread = new Thread(runnable);
thread.start();
```

**Lambda Expression:**

```java
Thread thread = new Thread(() -> {
    // Task to be executed by the thread
});
thread.start();
```
