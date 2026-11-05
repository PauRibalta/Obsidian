## 1. Parallelism and Multitasking

**Multitasking** allows different tasks to evolve logically in parallel. This may correspond to physical parallelism.

- **Maximum physical parallelism:** each parallel task has its own physical processor.
- Otherwise, tasks share processors and are executed according to a **scheduler**, whose decisions are generally outside the programmer's control.

### Processes vs Threads

|Process|Thread|
|---|---|
|An executable program loaded into memory|A sequential logical activity|
|Has its own address space|Shares the process address space with other threads|
|Contains variables and data structures|Communicates through shared variables|
|Processes communicate through the OS, files, or network|Has its own execution context, including program counter and local variables|
|Can contain multiple threads|Often called a _lightweight process_|

Concurrency reflects the logical structure of a problem: components may interact as separate processes, while each component may use multiple threads. For example, a web server can handle multiple browser requests using multiple threads.

## 2. Creating Threads in Java

### Method 1: Extend `Thread`

1. Define a class that extends `Thread`.
2. Implement the `run()` method with the code to execute.
3. Create an instance and call `start()`.

```
class ListSorter extends Thread {
    public void run() {
        // Statements executed by the thread
    }
}

ListSorter sorter = new ListSorter();
sorter.start();
```

**Important:** `start()` launches a new thread and returns immediately. The thread executes `run()`; calling `run()` directly does not start a separate thread.

Different threads may interleave their output in different ways because scheduling is not controlled by the programmer. The order of execution is therefore not deterministic.

### Method 2: Implement `Runnable`

`Thread` implements the `Runnable` interface, which defines the `run()` method.

```
class ListSorter implements Runnable {
    public void run() {
        // Statements executed by the thread
    }
}

ListSorter sorter = new ListSorter();
Thread myThread = new Thread(sorter);
myThread.start();
```

This approach is more general because the task class can still extend another class.

## 3. Shared Data and Interference

When multiple threads access shared data, sequences of operations that must preserve consistency need **mutual exclusion**.

Consider a shared bank account:

```
class BankAccount {
    private float balance;

    public BankAccount(float initialBalance) {
        balance = initialBalance;
    }

    public void deposit(float amount) {
        balance += amount;
    }

    public void withdraw(float amount) {
        balance -= amount;
    }
}
```

The operations `balance += amount` and `balance -= amount` are not necessarily atomic. An increment or decrement conceptually involves:

1. Reading the current value.
2. Reading the amount.
3. Calculating the result.
4. Writing the result.

Concurrent interleaving may cause interference: the operation may increment the value, leave it unchanged, or decrement it, depending on the interleaving of operations.

### Atomic Sequences

An **atomic sequence** is a sequence of instructions that executes in isolation, without interleaving with instructions from competing sequences.

The goal is to protect critical sections that access or modify shared data.

## 4. Synchronization with `synchronized`

Java's `synchronized` keyword provides mutual exclusion.

```
class BankAccount {
    private float balance;

    public BankAccount(float initialBalance) {
        balance = initialBalance;
    }

    public synchronized void deposit(float amount) {
        balance += amount;
    }

    public synchronized void withdraw(float amount) {
        balance -= amount;
    }
}
```

### Intrinsic Locks (Monitors)

Java associates an **intrinsic lock**, also called a _monitor_, with every object.

When a `synchronized` instance method is invoked:

- If no thread holds the object's lock, the calling thread acquires it and executes the method.
- If another thread holds the lock, the caller is suspended until the lock becomes available.
- The lock is automatically released when the method returns, normally or exceptionally.

Different invocations of synchronized methods on the **same object** cannot execute concurrently.

```
class SynchronizedCounter {
    private int c = 0;

    public synchronized void increment() {
        c++;
    }

    public synchronized void decrement() {
        c--;
    }

    public synchronized int value() {
        return c;
    }
}
```

### Important Lock Rules

- Synchronized methods on the same object share the same instance lock.
- Synchronized methods on different objects use different instance locks.
- Constructors cannot be declared `synchronized`.
- Avoid exposing an object reference before construction is complete.
- Fields declared `final` and never modified after construction can be read using non-synchronized methods, according to the slides.
- The lock is released when the synchronized method exits, including when an uncaught exception causes it to exit.

### Static Synchronized Methods

A `static synchronized` method locks the `**Class**` **object** associated with the class, not an individual instance.

Consequently, synchronization of static data uses a lock distinct from the locks associated with class instances.

### Synchronized Statements

A synchronized statement allows the programmer to specify exactly which object to lock.

```
public void addName(String name) {
    synchronized (this) {
        lastName = name;
        nameCount++;
    }

    nameList.add(name);
}
```

The lock is released before `nameList.add(name)` executes. This can be useful when a later operation might need to wait for another lock, reducing potential liveness problems.

Different locks can protect independent data:

```
private long c1 = 0, c2 = 0;
private final Object lock1 = new Object();
private final Object lock2 = new Object();

public void inc1() {
    synchronized (lock1) {
        c1++;
    }
}

public void inc2() {
    synchronized (lock2) {
        c2++;
    }
}
```

This permits the two operations to proceed independently. However, fine-grained synchronization requires careful design.

## 5. Liveness Problems

**Liveness** is the property that a concurrent application makes progress within acceptable time limits.

Three important problems are:

|Problem|Definition|
|---|---|
|**Deadlock**|Two or more threads are blocked indefinitely, waiting for one another|
|**Starvation**|A thread has difficulty accessing a shared resource and therefore struggles to make progress|
|**Livelock**|Threads repeatedly perform operations without making effective progress|

### Deadlock Example: Friends Bowing

Suppose `Anna` and `Bob` each have a synchronized `bow()` method that calls the other person's synchronized `bowBack()` method.

```
public synchronized void bow(Friend other) {
    other.bowBack(this);
}

public synchronized void bowBack(Friend other) {
    // Return the bow
}
```

If Anna holds her lock while calling Bob, and Bob simultaneously holds his lock while calling Anna, each waits for the other's lock. Neither can proceed: this is a deadlock.

### Starvation

A thread is repeatedly delayed in accessing a shared resource. For example, other threads may frequently execute long methods, or a priority-based scheduler may continually favour other tasks.

### Livelock

Threads continue reacting to each other but do not advance the computation. The slides illustrate this with two people repeatedly saying “you go first”.

## 6. Guarded Blocks: `wait()`, `notify()` and `notifyAll()`

A **guarded block** allows a thread to wait until a condition becomes true.

Example: prevent a bank account withdrawal from making the balance negative.

```
public synchronized void withdraw(float amount)
        throws InterruptedException {
    while (balance - amount < 0) {
        wait();
    }

    balance -= amount;
}
```

### How `wait()` Works

- `wait()` releases the object's intrinsic lock.
- The calling thread suspends until it is notified or otherwise awakened.
- After waking, it must reacquire the lock before continuing.

The condition must be checked in a `**while**` **loop**, not an `if` statement. A thread can wake up even though the condition is still false (_spurious wakeup_), or another thread may have changed the state before the awakened thread reacquires the lock.

### How to Wake Waiting Threads

```
public synchronized void deposit(float amount) {
    balance += amount;
    notify();
}
```

- `notify()` wakes one waiting thread, if any. The choice is not deterministic.
- `notifyAll()` wakes all threads waiting on that object's monitor. They then compete to reacquire the lock; only one can hold it at a time.
- The awakened thread must check its condition again.

`wait()`, `notify()` and `notifyAll()` are used with the object's monitor, typically inside synchronized code.

### Shared FIFO Queue

A shared FIFO queue can use guarded blocks:

- **Insertion:** wait while the queue is full; after insertion, notify waiting threads.
- **Extraction:** wait while the queue is empty; after extraction, notify waiting threads.

The producer-consumer pattern follows the same principle.

### Producer-Consumer Example

A single message slot can be represented by a message and a boolean `empty`:

```
public synchronized String take()
        throws InterruptedException {
    while (empty) {
        wait();
    }

    empty = true;
    notifyAll();
    return message;
}

public synchronized void put(String message)
        throws InterruptedException {
    while (!empty) {
        wait();
    }

    empty = false;
    this.message = message;
    notifyAll();
}
```

- The consumer waits while the slot is empty.
- The producer waits while the slot is occupied.
- State changes are protected by synchronization.
- `notifyAll()` allows waiting threads to compete to proceed.

## 7. Synchronization and Inheritance

The `synchronized` modifier is treated as an implementation detail, not part of a method's interface. It is **not inherited** as a requirement.

A subclass that overrides a synchronized method can choose whether to declare the overriding method `synchronized`.

However, if the superclass method was synchronized, the slides recommend normally keeping the synchronization when reimplementing it, unless there is a deliberate reason not to.

## 8. Thread Life Cycle

The slides distinguish these thread states and transitions:

- **Initial:** a thread object has been created but not started.
- **Ready:** ready to execute.
- **Running:** executing its `run()` method.
- **Blocked:** waiting to acquire a lock.
- **Waiting:** waiting for another thread to perform an action, such as calling `notify()` or `notifyAll()`.
- **Sleeping:** temporarily inactive until the sleep interval elapses.
- **Finished/Stopped:** the `run()` method has ended.

Typical transitions include:

- `start()` activates a thread.
- Losing its time slice can move a running thread back to ready.
- Requesting an unavailable lock leads to blocked.
- `wait()` leads to waiting; notification allows the thread to compete to resume.
- `sleep()` leads to sleeping; elapsed time allows the thread to become ready.
- Completion of `run()` leads to the finished state.

**A** `**Thread**` **object can be started only once.** Once its `run()` method has finished, that same thread cannot be restarted.

## 9. Mutable Objects and Thread Safety

Synchronization of individual methods does not guarantee that a sequence of method calls observes a consistent object state.

Consider a mutable RGB object with synchronized methods `getRGB()` and `getName()`:

```
int myColorInt = color.getRGB();      // Statement 1
String myColorName = color.getName(); // Statement 2
```

If another thread calls `set()` between these statements, the integer colour and name may refer to different states. Each method may be synchronized correctly, but the **combined sequence** is not atomic.

This is a problem caused by object mutability and the separation between the two calls.

### Creating Immutable Objects

The slides give the following guidelines:

1. Do not provide setters that modify the object's state.
    
2. Declare instance fields `private` and `final`.
    
3. Prevent subclasses from overriding methods, for example by declaring the class `final` or using a private constructor with factory methods.
    
4. If fields refer to mutable objects:
    
    - Do not provide methods that modify those objects.
    - Do not share references to mutable objects.
    - Do not retain references to externally supplied mutable objects; make defensive copies when necessary.
    - Do not return internal mutable objects directly; return copies when necessary.

### Immutable RGB Example

An immutable RGB class stores final fields and does not provide a mutating `set()` method. Instead, `invert()` creates and returns a **new object**.

```
final public class ImmutableRGB {
    private final int red;
    private final int green;
    private final int blue;
    private final String name;

    public ImmutableRGB(int red, int green, int blue,
                        String name) {
        check(red, green, blue);
        this.red = red;
        this.green = green;
        this.blue = blue;
        this.name = name;
    }

    public int getRGB() {
        return (red << 16) | (green << 8) | blue;
    }

    public String getName() {
        return name;
    }

    public ImmutableRGB invert() {
        return new ImmutableRGB(
            255 - red,
            255 - green,
            255 - blue,
            "Inverse of " + name
        );
    }
}
```

The class is `final`, and its fields are `private final`. Since the object cannot be modified after construction, readers can safely observe its stable state without synchronizing each getter.

## 10. Advanced Concurrency: `java.util.concurrent`

The package `java.util.concurrent` provides higher-level concurrency tools, including:

- Lock objects.
- Executors and thread pools.
- Concurrent collections.
- Atomic variables.

### Lock Objects

The `Lock` interface, in `java.util.concurrent.locks`, offers explicit locking mechanisms beyond intrinsic `synchronized` locks.

The slides highlight:

- `tryLock()`: attempts to acquire a lock and can return immediately if it is unavailable, or use a specified timeout.
- `lockInterruptibly()`: permits the waiting thread to withdraw from a lock request if it is interrupted before acquiring the lock.

These mechanisms can help avoid waiting indefinitely for locks.

### Deadlock Avoidance with `ReentrantLock`

The friends-bowing example can use `ReentrantLock` objects and `tryLock()` instead of synchronized methods.

The method attempts to acquire both friends' locks. If it cannot acquire both, it releases any lock it acquired and abandons that attempt.

```
boolean myLock = false;
boolean yourLock = false;

try {
    myLock = lock.tryLock();
    yourLock = other.lock.tryLock();
} finally {
    if (!(myLock && yourLock)) {
        if (myLock) {
            lock.unlock();
        }
        if (yourLock) {
            other.lock.unlock();
        }
    }
}
```

The idea is to avoid waiting indefinitely while holding only one of the two locks. Locks successfully acquired for the operation must also be released appropriately after use.

### `ReadWriteLock`

`ReadWriteLock` distinguishes read operations from write operations:

- **Write lock:** exclusive; required for writing.
- **Read lock:** multiple readers may proceed concurrently, provided no write operation is taking place.

This is useful when reads are frequent and writes must remain exclusive.

## 11. Executors and Thread Pools

Creating a `Thread` for every task closely couples the task (`Runnable`) to its execution thread. In larger applications, these concepts can be separated using executors.

Executors provide efficient task management and reduce the overhead of creating and managing threads.

### Executor Interfaces

|Interface|Role|
|---|---|
|`Executor`|Basic interface for executing tasks|
|`ExecutorService`|Extends `Executor`; supports tasks that can produce results and provides additional task-management operations|
|`ScheduledExecutorService`|Extends `ExecutorService`; supports scheduled or periodic task execution|

### Using `Executor`

Instead of manually creating a thread:

```
(new Thread(r)).start();
```

submit the `Runnable` to an executor:

```
x.execute(r);
```

Here, `r` is a `Runnable` and `x` is an `Executor`.

### Using `ExecutorService` and `Future`

For a task that returns a result, use a `Callable<T>`:

```
Future<T> future = executor.submit(callable);
T result = future.get();
```

- `submit()` returns a `Future<T>`.
- `Future.get()` returns the result when it is available.
- If the result is not yet available, `get()` blocks the calling thread until it becomes available.

### Thread Pools

Executor implementations commonly use thread pools.

A **fixed thread pool** can be created using `Executors.newFixedThreadPool()`. Tasks are submitted to a queue and executed by the pool's available threads, avoiding the need to create a new thread for every task.

## 12. Concurrent Collections

The `java.util.concurrent` package provides collections designed for concurrent use.

### `BlockingQueue`

- FIFO queue.
- Insertion into a full queue or extraction from an empty queue can block.
- Operations can also use timeouts.

This can simplify producer-consumer implementations.

### `ConcurrentMap`

Supports atomic conditional operations, including:

- Removing or modifying a key-value pair only if the key is present.
- Adding a key-value pair only if the key is absent.

These operations avoid certain race conditions that would occur if the check and modification were separate operations.

## 13. Atomic Variables

The `java.util.concurrent.atomic` package provides classes for atomic operations on individual variables.

Atomic classes provide `get()` and `set()` methods corresponding to reading and writing their values. They also offer more complex atomic operations, such as `compareAndSet()`.

### Atomic Counter

```
import java.util.concurrent.atomic.AtomicInteger;

class AtomicCounter {
    private AtomicInteger c = new AtomicInteger(0);

    public void increment() {
        c.incrementAndGet();
    }

    public void decrement() {
        c.decrementAndGet();
    }

    public int value() {
        return c.get();
    }
}
```

- `incrementAndGet()` atomically increments the value and returns the updated value.
- `decrementAndGet()` atomically decrements the value and returns the updated value.
- `get()` reads the current value.

Atomic variables can avoid the need for synchronized methods in simple cases such as a counter, thereby avoiding liveness problems that may arise from locking.

## Exam Essentials

- **Process vs thread:** processes have separate address spaces; threads in a process share memory but have separate execution contexts.
    
- `**Thread**` **vs** `**Runnable**`**:** extending `Thread` defines a thread; implementing `Runnable` separates the task from the thread.
    
- `**start()**` **vs** `**run()**`**:** `start()` launches a new thread; `run()` contains its code.
    
- **Race conditions:** compound operations such as `x++` are not necessarily atomic.
    
- `**synchronized**`**:** provides mutual exclusion through an object's intrinsic lock.
    
- **Instance vs static synchronization:** instance methods lock an object; static methods lock the associated `Class` object.
    
- `**wait()**` **/** `**notify()**` **/** `**notifyAll()**`**:** `wait()` releases the monitor; notification wakes waiting threads, which must reacquire the lock.
    
- **Always test guarded conditions with** `**while**`**, not** `**if**`**.**
    
- **Deadlock, starvation, livelock:** distinguish indefinite mutual waiting, lack of fair resource access, and repeated actions without progress.
    
- **Immutability:** prevents state changes after construction; useful for consistent sharing between threads.
    
- `**Lock**` **and** `**ReadWriteLock**`**:** explicit locking and concurrent reads with exclusive writes.
    
- **Executors and thread pools:** separate task submission from thread management; `Future.get()` waits for a result.
    
- **Concurrent collections and atomic variables:** provide thread-safe operations without manually synchronizing every operation.