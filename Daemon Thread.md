# **Daemon Thread in Java**

A **daemon thread** in Java is a thread that runs in the background and is typically used for tasks that should not prevent the JVM from exiting when all user threads (non-daemon threads) finish their execution. Daemon threads are generally used for background tasks such as garbage collection, monitoring, or performing housekeeping operations.
## **Definition**

A daemon thread is a low-priority thread that provides services to user threads for background supporting tasks. It is marked as a daemon thread using the `setDaemon(true)` method.

## **Syntax**

To create a daemon thread, you use the `Thread` class and call the `setDaemon(true)` method on the thread object before starting the thread:

```java
Thread thread = new Thread(new RunnableTask());
thread.setDaemon(true);
thread.start();
```

## **Example**

Here's a simple example demonstrating how to create and use a daemon thread:

```java
public class DaemonThreadExample {
    public static void main(String[] args) {
        Thread daemonThread = new Thread(() -> {
            while (true) {
                try {
                    System.out.println("Daemon thread running");
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });

        daemonThread.setDaemon(true);
        daemonThread.start();

        try {
            Thread.sleep(5000); // Main thread sleeps for 5 seconds
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("Main thread ending");
    }
}
```

In this example, the daemon thread will print "Daemon thread running" every second, but it will terminate automatically when the main thread finishes its execution.

## **Advantages**

1. **Background Processing**: Ideal for background tasks such as monitoring and housekeeping.
2. **Automatic Termination**: Daemon threads do not prevent the JVM from exiting. When all user threads finish, daemon threads are terminated automatically.
3. **Resource Management**: Helps in managing resources efficiently by running low-priority background tasks.

## **Disadvantages**

1. **No Guaranteed Completion**: Daemon threads may not complete their tasks if the JVM exits before they finish.
2. **No Exception Handling**: If a daemon thread throws an exception, it may go unnoticed since there may not be any user thread to handle it.
3. **Not Suitable for Critical Tasks**: Since they can be terminated abruptly, daemon threads are not suitable for tasks that require guaranteed completion or critical resource management.

## **Use Cases**

1. **Garbage Collection**: The JVM's garbage collector is a daemon thread that automatically cleans up unused objects.
2. **Background Monitoring**: Tasks like logging, monitoring system health, or performance metrics.
3. **Resource Cleanup**: Periodically cleaning up resources, such as temporary files or connections.

## **Detailed Example**

Below is a more detailed example showing a user thread and a daemon thread working together:

```java
public class DaemonThreadExample {
    public static void main(String[] args) {
        Thread userThread = new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                System.out.println("User thread running: " + i);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println("User thread finished");
        });

        Thread daemonThread = new Thread(() -> {
            while (true) {
                System.out.println("Daemon thread running in background");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });

        daemonThread.setDaemon(true);

        userThread.start();
        daemonThread.start();

        try {
            userThread.join(); // Wait for the user thread to finish
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("Main thread ending");
    }
}
```

### **Example Output**

```
User thread running: 0
Daemon thread running in background
User thread running: 1
Daemon thread running in background
User thread running: 2
Daemon thread running in background
User thread running: 3
Daemon thread running in background
User thread running: 4
Daemon thread running in background
User thread finished
Main thread ending
```

In this example, the daemon thread runs indefinitely in the background, but it is terminated automatically when the main thread finishes its execution. The user thread runs for a finite number of iterations and completes, demonstrating how daemon threads support user threads without blocking the JVM from exiting.

## **Additional Resources**

- [Java Thread Documentation](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html)
- [Daemon Threads in Java](https://www.baeldung.com/java-daemon-thread)
- [Multithreading in Java](https://www.geeksforgeeks.org/multithreading-in-java/)
