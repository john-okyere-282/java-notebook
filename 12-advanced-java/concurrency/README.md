# Concurrency

## Processes vs Threads

| | Process | Thread |
|-|---------|--------|
| Memory | Own isolated memory space | Shares memory with other threads in the same process |
| Creation cost | High (OS must allocate new memory space) | Low (shares process memory) |
| Communication | IPC (sockets, pipes, shared memory) | Shared objects in heap |
| Failure isolation | A crashed process does not affect others | An uncaught exception in one thread can crash the whole process |

A Java program is one process. Within it, you can spawn multiple threads that run concurrently.

---

## Creating Threads

### 1. Extend Thread

```java
class PrintTask extends Thread {
    private final String message;

    PrintTask(String message) { this.message = message; }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + ": " + message);
    }
}

Thread t = new PrintTask("Hello");
t.start();  // start() creates the new thread and calls run() — never call run() directly
```

### 2. Implement Runnable (preferred over extending Thread)

```java
Runnable task = () -> System.out.println(Thread.currentThread().getName() + " running");
Thread t = new Thread(task);
t.start();
```

### 3. Implement Callable (returns a result)

```java
import java.util.concurrent.Callable;
import java.util.concurrent.FutureTask;

Callable<Integer> task = () -> {
    Thread.sleep(1000);
    return 42;
};

FutureTask<Integer> future = new FutureTask<>(task);
new Thread(future).start();
Integer result = future.get(); // blocks until done; throws checked exceptions
```

---

## Thread Lifecycle

```
            start()
NEW ──────────────────► RUNNABLE
                           │
              scheduled ◄──┘──► waiting for I/O, sleep, wait()
              by OS      BLOCKED/
                         WAITING
                           │
                           ▼
                       TERMINATED
                       (run() returns)
```

---

## synchronized — Mutual Exclusion

When multiple threads access a shared mutable variable, results are unpredictable (a **race condition**). `synchronized` ensures only one thread executes a block at a time.

### Synchronized method

```java
public class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;   // read-modify-write is now atomic
    }

    public synchronized int getCount() {
        return count;
    }
}
```

### Synchronized block (finer-grained locking)

```java
public class Counter {
    private int count = 0;
    private final Object lock = new Object();

    public void increment() {
        synchronized (lock) {
            count++;
        }
    }
}
```

---

## volatile — Visibility Guarantee

`volatile` ensures that a variable's value is always read from and written to main memory, not a CPU cache. It prevents visibility bugs but does not make compound operations (like `count++`) atomic.

```java
// Without volatile, one thread's update may not be visible to another thread
private volatile boolean running = true;

public void stop() {
    running = false;  // change visible to all threads immediately
}

public void run() {
    while (running) {
        // do work
    }
}
```

---

## Race Conditions and Deadlocks

### Race condition

```java
// BAD — two threads reading and writing count without synchronization
class UnsafeCounter {
    int count = 0;
    void increment() { count++; }  // read-modify-write — not atomic!
}
```

Thread A reads `count = 5`, Thread B reads `count = 5`, both write `6` → lost update.

### Deadlock

```java
// Thread 1 holds lockA, waits for lockB
// Thread 2 holds lockB, waits for lockA — neither can proceed

Object lockA = new Object();
Object lockB = new Object();

// Thread 1
synchronized (lockA) {
    synchronized (lockB) { /* work */ }
}

// Thread 2
synchronized (lockB) {        // acquires lockB
    synchronized (lockA) { /* stuck — Thread 1 holds lockA */ }
}
```

**Prevention:** Always acquire locks in the same order across all threads.

---

## ExecutorService — Thread Pools

> **AmaliTech §5.1:** Use an `ExecutorService` instead of creating raw threads. Creating a new `Thread` for every task is wasteful and uncontrolled.

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

// Fixed pool — at most 4 threads running at once; excess tasks queue up
ExecutorService executor = Executors.newFixedThreadPool(4);

for (int i = 0; i < 10; i++) {
    final int taskId = i;
    executor.submit(() -> {
        System.out.println("Task " + taskId + " on " + Thread.currentThread().getName());
    });
}

// Shut down — no new tasks accepted; existing tasks complete
executor.shutdown();
executor.awaitTermination(10, TimeUnit.SECONDS);
```

| Factory method | Description |
|----------------|-------------|
| `newFixedThreadPool(n)` | Fixed n threads; queues excess tasks |
| `newCachedThreadPool()` | Creates threads on demand; reuses idle ones; no bound |
| `newSingleThreadExecutor()` | One thread; tasks execute sequentially |
| `newScheduledThreadPool(n)` | Runs tasks after a delay or periodically |

---

## CompletableFuture — Asynchronous Chaining

> **AmaliTech §5.2:** Use `CompletableFuture` for composing asynchronous operations.

```java
import java.util.concurrent.CompletableFuture;

CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> fetchUser(userId))           // runs on ForkJoinPool
    .thenApply(user -> fetchOrders(user.getId()))   // transforms the result
    .thenApply(orders -> formatSummary(orders))     // transforms again
    .exceptionally(ex -> "Error: " + ex.getMessage()); // handle failures

String result = future.get(); // block and get final value
```

### Combining multiple futures

```java
CompletableFuture<String> userFuture    = CompletableFuture.supplyAsync(() -> fetchUser(1L));
CompletableFuture<String> addressFuture = CompletableFuture.supplyAsync(() -> fetchAddress(1L));

// Run both in parallel, combine when both complete
CompletableFuture<String> combined = userFuture.thenCombine(
    addressFuture,
    (user, address) -> user + " lives at " + address
);

// Wait for all futures (no result)
CompletableFuture.allOf(userFuture, addressFuture).join();
```

---

## Thread-Safe Collections and Atomic Types

> **AmaliTech §5.3:** Share as little mutable state as possible between threads. When sharing is necessary, use thread-safe types.

### AtomicInteger — lock-free counter

```java
import java.util.concurrent.atomic.AtomicInteger;

AtomicInteger counter = new AtomicInteger(0);

// incrementAndGet is atomic — no synchronization needed
int newValue = counter.incrementAndGet();

// Compare-and-swap — atomically set to 10 only if current value is 5
boolean updated = counter.compareAndSet(5, 10);
```

### ConcurrentHashMap — thread-safe map

```java
import java.util.concurrent.ConcurrentHashMap;

ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
map.put("Alice", 1);

// Atomic update — thread-safe increment
map.merge("Alice", 1, Integer::sum);  // Alice → 2
```

### Other thread-safe collections

| Class | Description |
|-------|-------------|
| `ConcurrentHashMap` | Thread-safe HashMap; full concurrency for reads |
| `CopyOnWriteArrayList` | Thread-safe list; copies on every write; good for read-heavy workloads |
| `BlockingQueue` | Thread-safe queue with blocking `put` and `take` — ideal for producer-consumer |
| `AtomicLong`, `AtomicBoolean`, `AtomicReference` | Lock-free atomic types |

---

## Virtual Threads (Java 21)

Virtual threads are lightweight threads managed by the JVM rather than the OS. Creating millions of them is cheap (each uses only a few KB of heap). They are designed for high-throughput concurrent code that does a lot of I/O.

```java
// Create a virtual thread
Thread vt = Thread.ofVirtual().start(() -> System.out.println("Virtual thread!"));

// ExecutorService backed by virtual threads
try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
    for (int i = 0; i < 100_000; i++) {
        final int id = i;
        executor.submit(() -> System.out.println("Task " + id));
    }
}  // auto-closes
```

Virtual threads are ideal for server applications where each request handler is a thread that spends most of its time waiting for I/O (database queries, HTTP calls). They make blocking I/O as scalable as reactive programming, but with much simpler code.

> **Note:** Virtual threads do not speed up CPU-bound work — use `ForkJoinPool` for that.
