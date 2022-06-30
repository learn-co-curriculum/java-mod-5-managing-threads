# Lesson Title

## Learning Goals

- Use different methods to control threads.

## Introduction

We’ll look at some of the common methods used for managing threads in Java.
After you start a thread, it’s sometimes necessary to control their state.

## Sleeping Threads

The `Thread.sleep` method causes the currently executing thread to suspend its
execution for a specified amount of time (usually in milliseconds). This frees
up CPU time for other threads that are running in the process. Let’s look at an
example.

```java
class Example {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            System.out.println("hello from the thread!");
        });

        t.start();
        System.out.println("started");

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            System.out.println(e.getMessage());
        }

        System.out.println("done");
    }
}
```

```java
started
Hello from the thread!
done // printed after at least 1000 milliseconds
```

The `Thread.sleep` method throws the checked exception `InterruptedException`.
In this code, we are telling the thread to be suspended for 1000 milliseconds.
This time is the minimum amount of time the thread will be suspended. Sometimes
the thread may be suspended for longer than 1000 milliseconds.

## Joining Threads

The `join` instance method on threads causes the current thread to wait for
completion of its execution before executing another thread. If we run the
following code without the `t.join()` statement, the “done” string will be
printed before the `t` thread finishes execution.

```java
class Example {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            try {
                System.out.println("hello from the thread!");
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                System.out.println(e.getMessage());
            }
        });

        System.out.println("start");
        t.start();
//        t.join();
        System.out.println("done");
    }
}
```

```java
start
done
hello from the thread!
```

Let’s run the code again with the `t.join` statement.

```java
class Example {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            try {
                System.out.println("hello from the thread!");
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                System.out.println(e.getMessage());
            }
        });

        System.out.println("start");
        t.start();
        t.join();
        System.out.println("done");
    }
}
```

```java
start
hello from the thread!
done
```

This time the program waits for the `t` thread to complete its execution before
continuing execution of the `main` thread.

The `join` method can also take a time argument (milliseconds) which defines
the amount of time to wait before moving on to another thread. This prevents the
thread from running indefinitely.

```java
class Example {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            try {
                Thread.sleep(3000);
                System.out.println("hello from the thread!");
            } catch (InterruptedException e) {
                System.out.println(e.getMessage());
            }
        });

        System.out.println("start");
        t.start();
        t.join(1000);
        System.out.println("done");
    }
}
```

```java
start
done
hello from the thread!
```

In this case, the `t` thread is executing for 3000 milliseconds while the `join`
method is given an argument of 1000 milliseconds. The execution of the `t`
thread is paused after 1000 milliseconds and the `main` thread is executed
instead. Then the `t` thread is given time on the CPU again to complete
execution.

## Conclusion

We’ve learned how to suspend currently executing threads and how to wait for the
current thread to execute before running other threads. We’ll learn how to use
these methods to write safe, concurrent code in the later lessons.
