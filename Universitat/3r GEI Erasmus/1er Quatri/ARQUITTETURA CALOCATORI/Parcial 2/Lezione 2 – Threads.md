## 1. Introduction to Threads

A thread is a flow of control that can execute concurrently with other threads within the same process.

- When a program is launched, Linux creates a new process containing a single thread.
    
- This initial thread executes the program sequentially and is called the main thread.
    
- A process can create additional threads that execute concurrently.
    
- Each thread represents a sequence of instructions and starts executing from a function specified at its creation.
    

### Main characteristics

- Threads belong to a process and share its address space, except for their individual stacks.
    
- Each thread has its own execution flow.
    
- Information exchange between threads is easier because they share memory.
    
- Thread management requires fewer OS resources and data structures than process management.
    

A thread is also called a lightweight process because it provides concurrent execution with lower management costs than a traditional process.

## 2. Sequential and Concurrent Execution

Two activities, A and B, can execute sequentially or concurrently.

### Sequential execution

Two activities are sequential if their execution order can be predicted in advance by examining the program code:

- A always executes before B, or
    
- B always executes before A.
    

### Concurrent execution

Two activities are concurrent (or parallel) if their execution order cannot be predicted in advance:

- It is not possible to guarantee that A always executes before B.
    
- It is not possible to guarantee that B always executes before A.
    

The actual execution order depends on how the system schedules the activities.

## 3. Simulated and Real Parallelism

Concurrent execution can be implemented in two ways.

### Simulated parallelism

- Occurs on a single-processor system.
    
- Multiple processes or threads take turns using the same processor.
    
- The operating system switches between activities through time-sharing.
    
- Only one activity executes on the processor at a given instant.
    

### Real parallelism

- Occurs on a multiprocessor system.
    
- Multiple processes or threads execute simultaneously on different processors.
    
- It can provide higher system performance.
    

### Comparison

|Simulated parallelism|Real parallelism|
|---|---|
|Single processor.|Multiple processors.|
|Activities take turns executing.|Activities can execute simultaneously.|
|Uses time-sharing.|Uses different processors.|

Important considerations:

- From the user's perspective, both situations represent concurrent execution.
    
- The programming model remains the same, regardless of the number of processors.
    
- Even in multiprocessor systems, the number of threads can exceed the number of processors. In this case, parallelism is partly simulated.
    
- The principles discussed in the course are independent of the number of processors.
    

## 4. Thread Granularity

Thread granularity refers to the amount of instructions assigned to each thread.

The appropriate granularity depends on the system:

|Granularity|More suitable system|
|---|---|
|A few instructions per thread.|Single-processor system: \(n\) threads, 1 processor.|
|Hundreds or thousands of instructions per thread.|Multiprocessor system: \(n\) threads, \(n\) processors.|

Multithreading can therefore be applied to both single-processor and multiprocessor systems, with different levels of work assigned to each thread.

## 5. POSIX Threads (Pthreads)

There are several implementation models for threads. The course focuses on the POSIX standard.

POSIX (Portable Operating System Interface for Computing Environment) is a set of operating system application interfaces (APIs).

Main characteristics:

- Provides standard functions and services for interacting with the operating system.
    
- Applications using POSIX APIs are portable across operating systems that adopt the standard.
    
- Thread management functions and data types are declared in the header file:
    

```
#include <pthread.h>
```

pthread means POSIX thread.

## 6. Pthreads and Processes

A thread is always created within a process.

- A process can contain one, two or more threads.
    
- Threads execute concurrently and can be suspended independently of one another.
    
- For example, a thread waiting for an I/O event does not necessarily prevent other threads in the same process from executing.
    

### Process termination

When a process terminates, all its threads are forcibly terminated, regardless of their current execution point.

Therefore, it is necessary to ensure coherent thread termination:

- The process should not terminate before its threads have finished.
    
- The process must wait for the termination of the threads it has created when their completion is required.
    

## 7. Thread IDs, PID and TGID

Each pthread is associated with two identifiers:

- Thread ID (`pthread_t`): uniquely identifies a thread.
    
- Process ID (`pid_t`): identifies the process to which the thread belongs.
    

The function `getpid()` returns the PID of the process containing the executing thread. Therefore, all threads belonging to the same process obtain the same PID.

### 7.1. PID and TGID in Linux

Linux internally associates a different identifier with each process, including lightweight processes (threads).

However, POSIX requires all threads belonging to the same process to share the same PID.

To adapt these two models, Linux uses the concept of a Thread Group:

- Thread Group: the set of threads (lightweight processes) belonging to the same process.
    
- TGID (Thread Group ID): the PID of the thread group leader.
    
- The thread group leader is the first process in the group, containing the default thread.
    
- A single-threaded process has a TGID equal to its PID.
    

### 7.2. `gettid()` and `getpid()`

Each thread has a pair of identifiers: one identifying the thread and another identifying its thread group.

|Function|Single-threaded process|Multithreaded process|
|---|---|---|
|`gettid()`|Returns the thread ID, equal to the PID.|Returns a unique thread ID for each thread.|
|`getpid()`|Returns the process PID, corresponding to the TGID.|Returns the TGID, identical for all threads in the same group.|

Key distinction:

- `gettid()` identifies an individual thread.
    
- `getpid()` identifies the process (thread group) shared by its threads.
    

## 8. Creating Threads: `pthread_create()`

The function `pthread_create()` creates a new thread.

It is conceptually similar to `fork()`, but instead of creating an independent process, it creates a thread by specifying the function that the new thread must execute.

```
pthread_create(&tID1, NULL, &tf1, (void *) n);
```

A new thread:

- Starts executing from the beginning of the specified function.
    
- Executes independently of the current execution point of the process that created it.
    
- Can execute concurrently with the creating thread.
    

### 8.1. Parameters of `pthread_create()`

The function receives four parameters:

|Parameter|Meaning|
|---|---|
|`&tID1`|Address of a `pthread_t` variable where the new thread ID will be stored.|
|`NULL`|Pointer to thread attributes. `NULL` selects default attributes.|
|`&tf1`|Address of the function executed by the new thread (`thread_function`).|
|`(void *) n`|Argument passed to the thread function.|

Important rules:

- Only one argument can be passed directly to the thread function.
    
- The argument must be of type `void *`.
    
- To pass multiple parameters, create a `struct` containing them and pass its address.
    

## 9. Terminating Threads: `pthread_join()`

The function `pthread_join()` waits for the termination of a specific thread.

It is conceptually similar to `waitpid()` for processes.

```
pthread_join(tID1, NULL);
```

Main characteristics:

- The thread whose termination must be awaited has to be specified.
    
- It allows the creating process to wait until the specified thread finishes.
    
- It helps guarantee coherent termination of threads before the process terminates.
    

### 9.1. How a thread terminates

A thread executes a function and terminates when:

- It executes a `return` statement, or
    
- It reaches the end of the executable code of its function.
    

A thread can return an exit code to the process that created it.

- The exit code is the value returned by the thread function.
    
- The creating process receives it when it executes `pthread_join()`.
    
- The exit code is transferred through the second parameter of `pthread_join()`.
    

### 9.2. Parameters of `pthread_join()`

```
pthread_join(tID1, (void *) &thread_exit);
```

|Parameter|Meaning|
|---|---|
|`tID1`|ID of the thread whose termination must be awaited.|
|`(void *) &thread_exit`|Pointer to a variable that receives the thread's return value.|

The thread function has the following general structure:

```
void *tf1(void *arg) {
    /* Thread function code */

    return (void *) valore_di_ritorno;
}
```

The return type is `void *`, and the function receives one `void *` argument.

## 10. Example: Creating and Waiting for Two Threads

The following example creates two threads executing the same function.

```
#include <pthread.h>
#include <stdio.h>

void *tf1(void *tID) {
    int conta = 0;  // Local variable

    conta++;

    printf("sono il thread n: %d; conta = %d\n",
           (int)tID, conta);

    return NULL;
}

int main(void) {
    pthread_t tID1, tID2;

    pthread_create(&tID1, NULL, &tf1, (void *)1);
    pthread_create(&tID2, NULL, &tf1, (void *)2);

    pthread_join(tID1, NULL);
    pthread_join(tID2, NULL);

    return 0;
}
```

How it works:

1. Two thread identifiers are declared: `tID1` and `tID2`.
    
2. The first thread is created and receives argument `1`.
    
3. The second thread is created and receives argument `2`.
    
4. Both threads execute the same function `tf1()`.
    
5. Each thread initializes its local variable `conta` to zero and increments it.
    
6. The main thread waits for both threads using `pthread_join()`.
    

The two threads can execute in either order.

## 11. The Main Thread

When an executable program is launched, the process initially contains one thread.

This thread is called the main thread or default thread.

- Its execution flow is represented by the `main()` function.
    
- It can create additional threads using `pthread_create()`.
    
- Any thread can also create other threads.
    
- When a new thread is created, concurrent execution begins between the new thread and the thread that created it.
    

A process can therefore develop a hierarchy of threads, all belonging to the same process.

## 12. Threads and Memory Sharing

All threads belonging to the same process execute within the same address space.

However, each thread has its own stack.

### 12.1. Memory organization

|Memory element|Shared between threads?|Explanation|
|---|---|---|
|Local variables|No|Allocated on the individual thread's stack.|
|Stack|No|Each thread has its own independent stack at a different address.|
|Global variables|Yes|Stored in the data segment.|
|Static variables|Yes|Stored in the data segment, not on the stack.|
|Process address space|Yes|All threads execute within the same process memory.|

### 12.2. Consequences of memory sharing

Advantages:

- Information exchange between threads is easier.
    
- Threads can access shared data directly.
    

Disadvantages:

- Shared data can be modified by multiple threads.
    
- It becomes more difficult to guarantee the correctness of a multithreaded program.
    

The distinction between local, global and static variables is particularly important when understanding how threads interact.

## 13. Local Variables in Threads

A local variable declared inside a thread function is allocated on that thread's stack.

Therefore, each thread has its own independent copy of the variable.

### Example

```
void *tf1(void *tID) {
    int conta = 0;  // Local variable

    conta++;

    printf("sono il thread n: %d; conta = %d\n",
           (int)tID, conta);

    return NULL;
}
```

Suppose two threads execute this function:

```
pthread_create(&tID1, NULL, &tf1, (void *)1);
pthread_create(&tID2, NULL, &tf1, (void *)2);

pthread_join(tID1, NULL);
pthread_join(tID2, NULL);
```

Each thread creates its own local variable `conta`.

- Thread 1 initializes its `conta` to `0` and increments it to `1`.
    
- Thread 2 initializes its own `conta` to `0` and increments it to `1`.
    
- The two variables are independent.
    

### Possible execution sequences

Sequence 1:

```
sono il thread n: 1; conta = 1
sono il thread n: 2; conta = 1
```

Sequence 2:

```
sono il thread n: 2; conta = 1
sono il thread n: 1; conta = 1
```

The output order can change, but both threads print `conta = 1` because each has its own local variable.

## 14. Global Variables in Threads

A global variable is declared outside functions and stored in the data segment.

Unlike local variables, it is shared by all threads belonging to the same process.

### Example

```
#include <pthread.h>
#include <stdio.h>

int conta = 0;  // Global variable

void *tf1(void *tID) {
    conta++;

    printf("sono il thread n: %d; conta = %d\n",
           (int)tID, conta);

    return NULL;
}

int main(void) {
    pthread_t tID1, tID2;

    pthread_create(&tID1, NULL, &tf1, (void *)1);
    pthread_create(&tID2, NULL, &tf1, (void *)2);

    pthread_join(tID1, NULL);
    pthread_join(tID2, NULL);

    return 0;
}
```

How it works:

- Both threads access the same global variable `conta`.
    
- The initial value is `0`.
    
- Each thread increments the same variable.
    
- The first thread to execute the increment obtains `1`.
    
- The second thread obtains `2`.
    

### Possible execution sequences

Sequence 1:

```
sono il thread n: 1; conta = 1
sono il thread n: 2; conta = 2
```

Sequence 2:

```
sono il thread n: 2; conta = 1
sono il thread n: 1; conta = 2
```

The thread that obtains `1` or `2` depends on the execution order.

Key point: The variable is shared, so its value is not independently initialized for each thread.

## 15. Static Variables in Threads

A static variable declared locally inside a function has two important properties:

- It is visible only within the function where it is declared.
    
- It preserves its value between successive function calls.
    

Although declared locally, a static variable is not allocated on the stack. It is stored in the data segment and is shared by all threads executing the same function.

### Example

```
#include <pthread.h>
#include <stdio.h>

void *tf1(void *tID) {
    static int conta = 0;  // Static variable

    conta++;

    printf("sono il thread n: %d; conta = %d\n",
           (int)tID, conta);

    return NULL;
}

int main(void) {
    pthread_t tID1, tID2;

    pthread_create(&tID1, NULL, &tf1, (void *)1);
    pthread_create(&tID2, NULL, &tf1, (void *)2);

    pthread_join(tID1, NULL);
    pthread_join(tID2, NULL);

    return 0;
}
```

### Possible execution sequences

Sequence 1:

```
sono il thread n: 1; conta = 1
sono il thread n: 2; conta = 2
```

Sequence 2:

```
sono il thread n: 2; conta = 1
sono il thread n: 1; conta = 2
```

The behavior is the same as with a global variable: both threads share the same counter.

### Global vs Static vs Local Variables

|Property|Local variable|Global variable|Static local variable|
|---|---|---|---|
|Declaration|Inside a function.|Outside functions.|Inside a function, with `static`.|
|Memory location|Stack.|Data segment.|Data segment.|
|Shared between threads?|No.|Yes.|Yes.|
|Preserves value between function calls?|No.|Yes.|Yes.|
|Visibility|Function/block.|Global scope.|Function/block.|

Important distinction: A static local variable behaves like a global variable regarding memory sharing and persistence, but its visibility is limited to the function where it is declared.

## 16. Passing Arguments to Threads and Returning Values

A thread function receives a single argument of type `void *`.

This allows different types of data to be passed by using pointers and type conversions.

### Example

```
#include <pthread.h>
#include <stdio.h>

void *tf(void *arg) {
    // Convert void pointer to int pointer and retrieve value
    int i = *((int *)arg);

    printf("sono thread_function: valore argomento = %d\n", i);

    i++;

    return (void *)i;
}

int main(void) {
    int argomento = 10;
    int thread_exit;

    pthread_t tID1;

    pthread_create(&tID1, NULL, &tf, &argomento);

    pthread_join(tID1, (void *)&thread_exit);

    printf("sono il main: thread_exit = %d\n", thread_exit);

    return 0;
}
```

### How the argument is received

The thread receives the address of `argomento`:

```
pthread_create(&tID1, NULL, &tf, &argomento);
```

Inside the thread function:

```
int i = *((int *)arg);
```

This expression performs two operations:

1. `(int *)arg`: converts the `void *` pointer into an `int *` pointer.
    
2. `*((int *)arg)`: dereferences the pointer to retrieve the integer value.
    

Therefore, `i` receives the value `10`.

### How the return value is retrieved

The thread increments the local variable:

```
i++;
```

It then returns the result:

```
return (void *)i;
```

The main thread waits for its termination and retrieves the returned value:

```
pthread_join(tID1, (void *)&thread_exit);
```

The second parameter points to the variable where the thread's return value is stored.

### Execution sequence

```
sono thread_function: valore argomento = 10
sono il main: thread_exit = 11
```

Important: The example illustrates both directions of communication:

- `pthread_create()` passes an argument from the creating thread to the new thread.
    
- `pthread_join()` retrieves the return value from the terminated thread.
    

## 17. Threads vs Processes: Usage Considerations

Threads and processes have different characteristics depending on the requirements of a program.

### 17.1. Efficiency

- Creating a new process requires memory copying and more expensive management techniques.
    
- Threads require fewer resources and less operating system management.
    
- In general, threads are more efficient than processes.
    

### 17.2. Protection

- Threads share the same address space.
    
- A faulty thread can damage data used by other threads in the same process.
    
- Processes have separate address spaces and cannot directly damage one another's memory.
    

Therefore, processes provide better isolation and protection.

### 17.3. Flexibility

- A thread can execute only the code of the function associated with it.
    
- Changing the executable program is possible with a process.
    
- A child process can use `exec` to replace its entire executable program.
    

Therefore, processes provide greater flexibility when changing executable code.

### 17.4. Data sharing

- Threads share the same memory, making data exchange simple.
    
- Processes have separate address spaces.
    
- Communication between processes is more complicated and requires Inter-Process Communication (IPC) mechanisms.
    

### Overall comparison

|Aspect|Threads|Processes|
|---|---|---|
|Efficiency|Generally more efficient.|More expensive to create and manage.|
|Protection|Limited isolation; share memory.|Better isolation through separate address spaces.|
|Flexibility|Execute the associated thread function.|Can replace executable code using `exec`.|
|Data sharing|Simple through shared memory.|Requires IPC mechanisms.|

## Exam Essentials

### 1. Thread fundamentals

- A thread is a flow of control executing within a process.
    
- A process initially contains one thread: the main thread.
    
- A process can create multiple threads.
    
- Threads share the process address space, except for their individual stacks.
    
- Threads are also called lightweight processes.
    

### 2. Sequential vs Concurrent execution

- Sequential: the execution order of activities can be predicted.
    
- Concurrent: the execution order cannot be predicted in advance.
    
- Concurrent execution can be simulated through time-sharing or real through multiple processors.
    

### 3. POSIX and Pthreads

- POSIX defines standard operating system APIs.
    
- Thread functions and data types are declared in `<pthread.h>`.
    
- POSIX provides portability across operating systems adopting the standard.
    

### 4. Thread identification

|Function / Identifier|Purpose|
|---|---|
|`pthread_t`|Thread identifier type.|
|`pid_t`|Process identifier type.|
|`gettid()`|Returns the individual thread ID.|
|`getpid()`|Returns the process ID (TGID for a thread group).|
|TGID|Shared identifier of all threads in the same thread group.|

Remember that `gettid()` differs between threads, while `getpid()` is the same for all threads belonging to the same process.

### 5. Thread creation and termination

`pthread_create()`

```
pthread_create(&thread_ID, NULL, &thread_function, argument);
```

- Stores the new thread ID.
    
- Uses default attributes when the second parameter is `NULL`.
    
- Specifies the function to execute.
    
- Passes one `void *` argument.
    

`pthread_join()`

```
pthread_join(thread_ID, &thread_return_value);
```

- Waits for the specified thread to terminate.
    
- Can retrieve the value returned by the thread function.
    
- Helps ensure that the process does not terminate before its threads have finished.
    

### 6. Memory sharing

Know exactly which variables are shared:

- Local variables: private to each thread because they are stored on individual stacks.
    
- Global variables: shared by all threads.
    
- Static local variables: shared by all threads, preserve their values between function calls, but have local visibility.
    

### 7. Understand the output of multithreaded programs

Be able to determine:

- Whether a variable is private or shared.
    
- Whether each thread has its own copy of a variable.
    
- How many times a shared variable is incremented.
    
- Why the output order can change depending on thread execution order.
    

Typical distinction:

- Two threads with `int conta = 0;` inside the function each print `1`.
    
- Two threads with a shared global or static `conta` increment the same variable, producing `1` and `2` in an order that depends on execution.
    

### 8. Passing arguments and return values

Understand these operations:

```
pthread_create(&tID1, NULL, &tf, &argomento);
```

Passes the address of an argument to the thread.

```
int i = *((int *)arg);
```

Converts the pointer and retrieves the integer value.

```
return (void *)i;
```

Returns a value from the thread function.

```
pthread_join(tID1, (void *)&thread_exit);
```

Waits for the thread and retrieves its return value.

### 9. Threads vs Processes

Remember the four main comparison criteria:

- Efficiency: threads are generally cheaper.
    
- Protection: processes provide better isolation.
    
- Flexibility: processes can replace their executable using `exec`.
    
- Data sharing: threads share memory directly, while processes require IPC mechanisms.