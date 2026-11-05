## 1. Operating System (OS)

An **Operating System (OS)** is a set of service programs that acts as an intermediary between users, applications and the computer hardware.

It is **system software** that provides an environment in which application programs can execute.

### Main goals

- **Virtual machine:** provides users and applications with a simpler, abstract view of the physical machine.
- **Resource management:** manages the computer's physical resources and coordinates their use.
- **Parallelism:** allows multiple processes to execute concurrently while avoiding interference between them.
- **System services:** provides services to applications through system calls.

The OS manages access to resources such as:
- CPU.
- Main memory.
- Secondary storage.
- Input/output devices.
- Files and other system resources.

## 2. Sequential and Parallel Execution

### Sequential execution

In sequential execution, instructions and programs execute one after another.

Advantages:
- Programs do not interfere with each other during execution.
- The execution model is relatively simple.

Limitations:
- It cannot efficiently support multiple applications running at the same time.
- It is unsuitable for multi-user systems and servers handling multiple requests.
- It does not adequately support distributed applications.

### Parallel execution

Parallel execution allows multiple processes to execute concurrently.

The OS must balance two main objectives:

- **Parallelism:** allow several processes to make progress at the same time.
- **Virtualization:** provide each process with the impression that it has its own resources, avoiding interference with other processes.

The OS achieves this by creating and managing **processes**.

A process represents an independent execution environment for a program.

## 3. Resource Virtualization

Physical resources are often shared by several processes.

This can create conflicts when multiple processes request the same resource.

The OS manages these conflicts through **resource virtualization**.

### Main principles

- The OS controls access to real physical resources.
- It provides processes with virtual resources.
- When a resource is busy, requests can be organized into queues.
- The OS decides which process can use a resource and when.

### Access to peripherals

Applications do not normally access peripheral devices directly through hardware instructions.

Instead, they request operations through **system calls**.

The OS manages the actual interaction with the device.

This provides:
- Protection of hardware resources.
- Controlled access by multiple processes.
- An abstraction that simplifies application programming.

## 4. System Programming and System Calls

**System programming** involves writing programs that interact with the operating system and use its services.

A **system call** is the interface through which a program requests a service from the OS.

### Main categories of system calls

| Category | Purpose |
|---|---|
| Process management | Create, execute and terminate processes |
| Signals | Manage notifications and events |
| Files | Create, read, write and manage files |
| Directories and file systems | Manage directories and file-system operations |
| Protection | Control access to system resources |
| Time and date | Obtain and manage time-related information |

System calls allow applications to use OS services without directly managing the underlying hardware.

## 5. Processes

A **process** is a program in execution.

A program is a passive set of instructions stored in memory or on disk. A process is the active execution of that program.

Each process has:
- A unique **Process Identifier (PID)**.
- Its own execution state.
- Its own memory space.
- Information managed by the operating system.

### Process identification

Each process is identified by a PID.

According to the lecture, Linux assigns PIDs sequentially using a 16-bit identifier.

### Parent and child processes

Processes can create other processes.

- The process that creates another process is the **parent process**.
- The newly created process is the **child process**.
- Processes form a parent-child hierarchy.
- The initial system process is an exception to the normal parent-child creation mechanism.

### Process memory segments

A process is associated with different memory segments.

| Segment | Contents |
|---|---|
| Text / Code | Program instructions |
| User data | Variables and dynamically allocated memory |
| System data | Information managed by the OS |

#### User data

User data includes:

- **Global and static variables:** stored in the data area.
- **Local variables:** stored on the stack.
- **Dynamic memory:** allocated using functions such as `malloc()`.

#### System data

System data contains information managed by the OS.

For example:
- Open-file tables.
- Other information required to manage the process and its resources.

## 6. Main Process Management System Calls

The main process-related system calls covered in this lecture are:

| System call | Purpose |
|---|---|
| `fork()` | Creates a child process |
| `exit()` | Terminates the current process |
| `wait()` | Waits for any child process to terminate |
| `waitpid()` | Waits for a specific child process |
| `exec()` | Replaces the program executed by the current process |

Other useful functions:

| Function | Purpose |
|---|---|
| `getpid()` | Returns the PID of the current process |
| `getppid()` | Returns the PID of the parent process |

## 7. Creating Processes: `fork()`

The `fork()` system call creates a new process.

The newly created process is called the **child process**, while the process that called `fork()` is the **parent process**.

### Syntax

~~~c
pid_t fork(void);
~~~

### Behaviour

When `fork()` is called:

1. A new child process is created.
2. The child is initially a copy of the parent.
3. The parent and child have separate memory spaces.
4. Both processes continue executing the same program from the instruction immediately following `fork()`.

The child initially receives a copy of the parent's data and system information, with the return value of `fork()` being the important difference.

After creation, the two processes execute independently. Changes made to variables in one process do not modify the corresponding variables in the other process.

### Return value

The return value of `fork()` identifies which process is executing:

| Return value | Meaning |
|---|---|
| `> 0` | Parent process: returns the PID of the child |
| `0` | Child process |
| `-1` | Error: the child process was not created |

This return value allows the program to distinguish between the parent and child.

### Example

~~~c
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>

int main() {
    pid_t pid;

    pid = fork();

    if (pid == 0) {
        printf("Child process\n");
    } else if (pid > 0) {
        printf("Parent process\n");
    } else {
        printf("Error creating process\n");
    }

    return 0;
}
~~~

Both processes execute the instructions after `fork()`, but each follows a different branch depending on the returned value.

### Execution order

The parent and child execute independently.

The OS scheduler determines which process executes first.

Therefore:
- The parent may execute before the child.
- The child may execute before the parent.
- Their instructions may be interleaved.

**Never assume a fixed execution order after `fork()` unless synchronization is used.**

## 8. Getting Process Identifiers

Two functions allow a process to obtain its own PID and its parent's PID.

### `getpid()`

Returns the PID of the current process.

~~~c
pid_t getpid(void);
~~~

### `getppid()`

Returns the PID of the parent process.

~~~c
pid_t getppid(void);
~~~

### Example

~~~c
#include <stdio.h>
#include <unistd.h>

int main() {
    printf("PID: %d\n", getpid());
    printf("Parent PID: %d\n", getppid());

    return 0;
}
~~~

Each process obtains its own PID when it executes `getpid()`.

## 9. Multiple Calls to `fork()`

A process can create multiple child processes by calling `fork()` more than once.

However, it is important to understand that **every process that executes a `fork()` call creates a new child process**.

Consider the following structure:

~~~c
pid_t pid1, pid2;

pid1 = fork();

if (pid1 == 0) {
    /* First child */
} else {
    /* Parent */
    pid2 = fork();
}
~~~

In this example:

1. The original process creates the first child.
2. The first child enters the `if` branch.
3. The parent enters the `else` branch.
4. Only the parent executes the second `fork()`.
5. The second `fork()` creates another child.

The resulting processes are:
- Original parent.
- First child.
- Second child.

The first child does not execute the second `fork()` because it follows the `if` branch.

### Important rule

When analysing multiple `fork()` calls, always determine which processes reach each call.

Do not simply count the number of `fork()` instructions: determine how many processes execute each one.

## 10. Terminating Processes: `exit()`

The `exit()` function terminates the current process.

### Syntax

~~~c
void exit(int status);
~~~

### Behaviour

- Terminates the process that calls it.
- Returns an exit status to the parent process.
- Releases the resources associated with the terminated process through the OS.

### Exit status

The exit status communicates whether the process finished successfully or encountered an error.

| Exit status | Meaning |
|---|---|
| `0` | Successful termination |
| Non-zero | Error or abnormal result |

If the parent process has already terminated, the exit status is returned to the operating system's command interpreter.

A program can also terminate without an explicit call to `exit()`.

## 11. Waiting for a Child Process: `wait()`

The `wait()` system call allows a parent process to wait for the termination of one of its children.

### Syntax

~~~c
pid_t wait(int *status);
~~~

### Behaviour

- Suspends the calling process until one of its child processes terminates.
- If a child has already terminated, `wait()` returns immediately.
- It returns the PID of the terminated child.
- The child's termination status is stored in the memory location indicated by `status`.

### Return value

`wait()` returns the PID of the child process that terminated.

### Exit status encoding

According to the lecture, the child's exit code is stored in the status variable multiplied by 256.

Therefore, to recover the exit code:

~~~c
exit_code = status / 256;
~~~

### Example

~~~c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

int main() {
    pid_t pid;
    int status;

    pid = fork();

    if (pid == 0) {
        exit(5);
    } else {
        wait(&status);
        printf("Child exit code: %d\n", status / 256);
    }

    return 0;
}
~~~

The child terminates with exit code `5`.

The parent waits for the child and retrieves its termination status.

According to the lecture's encoding, the status value is `5 * 256`, so dividing it by 256 recovers the original exit code.

## 12. Zombie Processes

A **zombie process** is a child process that has terminated but whose termination status has not yet been collected by its parent.

### How a zombie is created

1. A child process terminates.
2. The parent has not yet called `wait()` to collect its termination status.
3. The child remains as a zombie.
4. Its termination information is retained so that the parent can retrieve it.

### How a zombie is removed

When the parent calls `wait()`:

- The termination status is collected.
- The zombie is cleaned up.
- `wait()` returns the PID of the terminated child.

### Two possible situations

| Situation | Behaviour |
|---|---|
| Child terminates before parent calls `wait()` | Child becomes a zombie until the parent collects its status |
| Parent is already waiting when child terminates | `wait()` receives the termination information and returns |

**Important:** A zombie has already terminated. It is not an executing process; only its termination information remains available.

## 13. Waiting for a Specific Child: `waitpid()`

The `waitpid()` system call is similar to `wait()`, but it allows the parent to specify which child process it wants to wait for.

### Syntax

~~~c
pid_t waitpid(pid_t pid, int *status, int options);
~~~

### Parameters

| Parameter | Meaning |
|---|---|
| `pid` | PID of the child process to wait for |
| `status` | Address where the child's termination status is stored |
| `options` | Specifies additional waiting options |

### Behaviour

- Waits for the child identified by the supplied PID.
- If that child has already terminated, the function returns immediately.
- The return value is the PID of the terminated child.
- The termination status is stored in the location pointed to by `status`.

### Example

~~~c
pid_t pid;
int status;

pid = fork();

if (pid == 0) {
    exit(5);
} else {
    waitpid(pid, &status, 1);
}
~~~

In this example, the parent waits for the child identified by `pid`.

The lecture uses `1` as the options argument in its example.

### `wait()` vs `waitpid()`

| `wait()` | `waitpid()` |
|---|---|
| Waits for any child process | Waits for a specified child process |
| Does not specify a child PID | Receives a PID as an argument |
| Returns the PID of the terminated child | Returns the PID of the selected terminated child |
| Retrieves the child's termination status | Retrieves the child's termination status |

## 14. Replacing a Program: `exec()`

The `exec()` family of system calls replaces the program currently being executed by a process with a new program.

**The process is not replaced: its program is replaced.**

### Main behaviour

When a process calls `exec()`:

1. The current program is replaced by a new program.
2. The code and data segments are replaced.
3. The process continues to exist.
4. The PID remains the same.
5. The system segment is not replaced, so resources such as open files and sockets remain open.
6. The new program starts executing.

The new program receives its arguments through `argc` and `argv`.

### Important distinction

| Property | After `exec()` |
|---|---|
| Process | Same process |
| PID | Unchanged |
| Code segment | Replaced |
| Data segment | Replaced |
| System segment | Not replaced |
| Open files and sockets | Remain open |

### Passing arguments to the new program

The new program receives arguments through:

- `argc`: number of arguments.
- `argv`: array of strings containing the arguments.

The first argument, `argv[0]`, is the program name.

## 15. The `execl()` Function

The lecture presents `execl()` as a way to execute a new program by specifying its path and arguments individually.

### Syntax

~~~c
int execl(
    char *path_programma,
    char *arg0,
    char *arg1,
    ...
    char *argn
);
~~~

### Parameters

| Parameter | Meaning |
|---|---|
| `path_programma` | Full path of the executable program |
| `arg0` | Name of the program |
| `arg1`, `arg2`, ... | Arguments passed to the new program |
| `NULL` | Marks the end of the argument list |

The final argument must always be `NULL`.

### Important rules

- The first parameter identifies the executable using its full path.
- `arg0` should contain the program name.
- The remaining arguments are passed to the new program.
- The argument list must end with `NULL`.

The lecture describes the return value as:
- `0` on success.
- `-1` on error.

## 16. Understanding `argc` and `argv`

When a program is executed, its `main()` function can receive command-line arguments.

### Syntax

~~~c
int main(int argc, char *argv[])
~~~

### Parameters

| Parameter | Meaning |
|---|---|
| `argc` | Number of arguments |
| `argv` | Array of pointers to argument strings |
| `argv[0]` | Program name |
| `argv[1]` | First argument |
| `argv[2]` | Second argument |

### Example

Suppose a program is executed with:

~~~c
main1 parametro1 parametro2
~~~

The program receives:

~~~c
argc = 3;

argv[0] = "main1";
argv[1] = "parametro1";
argv[2] = "parametro2";
~~~

Remember that `argc` includes the program name.

## 17. Example: Executing a Program with `execl()`

Consider a program called `exec1` that executes another program called `main1`.

~~~c
#include <unistd.h>

int main() {
    execl(
        "/esempi/main1",
        "main1",
        "parametro1",
        "parametro2",
        NULL
    );

    return 0;
}
~~~

The `execl()` call replaces the current program with `/esempi/main1`.

The new program receives:

~~~c
argc = 3;

argv[0] = "main1";
argv[1] = "parametro1";
argv[2] = "parametro2";
~~~

The new program executes in the same process.

Therefore:
- The PID remains unchanged.
- The original program is replaced.
- The new program starts with its own code and data.

## 18. Combining `fork()` and `exec()`

Using `exec()` alone replaces the current program.

This means that the original program does not continue executing after the replacement program finishes.

To execute another program while keeping the original process, the usual approach is to combine `fork()` and `exec()`.

### General procedure

1. The original process calls `fork()`.
2. A child process is created.
3. The child calls `exec()` to replace its program with the new program.
4. The parent continues executing its original program.
5. The parent can call `wait()` to wait for the child to terminate.

### Process structure

~~~c
pid_t pid;

pid = fork();

if (pid == 0) {
    /* Child process */
    execl("/path/program", "program", NULL);
} else {
    /* Parent process */
    wait(NULL);
    /* Continue original program */
}
~~~

### Why combine them?

| `exec()` alone | `fork()` + `exec()` |
|---|---|
| Replaces the current program | Creates a child and replaces the child's program |
| Original program does not continue as before | Parent keeps executing its original program |
| Same process and same PID | Child has a different PID from the parent |
| No separate parent process is created | Parent can wait for the child |

### PID behaviour

The child process keeps the PID it received when `fork()` created it.

Calling `exec()` does not create a new process, so the PID remains unchanged.

Therefore:
- Parent PID remains the same.
- Child PID remains the same before and after `exec()`.
- Parent and child have different PIDs.

### Example execution sequence

1. Parent calls `fork()`.
2. Child is created with a new PID.
3. Child calls `exec()`.
4. The child starts executing the new program, keeping its PID.
5. Parent continues executing the original program.
6. Parent calls `wait()` and suspends until the child terminates.
7. Child terminates.
8. Parent resumes execution.

This technique allows a program to execute another program while preserving the original parent process.

## 19. Exercise: Analysing Multiple Processes and Variables

Consider the following program:

~~~c
main()
{
    int i, j, k, m, stato;
    pid_t pid1, pid2;

    i = 10;
    j = 20;
    k = 30;
    m = 40;

    pid1 = fork();

    if (pid1 == 0) {
        /* FIRST CHILD */

        j = j + 1;

        pid2 = fork();

        if (pid2 == 0) {
            /* SECOND CHILD */

            k = k + 1;
            exit();
        }
        else {
            /* FIRST CHILD */

            m = m + 1;
            wait(&stato);
            exit();
        }
    }
    else {
        /* PARENT */

        i = i + 1;
        wait(&stato);
        exit();
    }
}
~~~

### 19.1. Process creation

The first `fork()` creates the first child.

- The parent executes the `else` branch.
- The first child executes the `if` branch.

The first child increments `j` and then executes the second `fork()`.

The second `fork()` is therefore executed only by the first child.

It creates the second child.

The resulting processes are:

| Process | Description |
|---|---|
| Parent | Original process |
| First child | Created by the first `fork()` |
| Second child | Created by the second `fork()` |

### 19.2. Variable independence

Each process has its own copy of the variables.

Changes in one process do not affect the variables of another process.

The initial values are:

| Variable | Initial value |
|---|---:|
| `i` | 10 |
| `j` | 20 |
| `k` | 30 |
| `m` | 40 |

### 19.3. Variable modifications

**Parent process**

The parent executes:

~~~c
i = i + 1;
~~~

Its variables become:

| Variable | Value |
|---|---:|
| `i` | 11 |
| `j` | 20 |
| `k` | 30 |
| `m` | 40 |

**First child**

The first child executes:

~~~c
j = j + 1;
~~~

Then it creates the second child and executes:

~~~c
m = m + 1;
~~~

Its variables become:

| Variable | Value |
|---|---:|
| `i` | 10 |
| `j` | 21 |
| `k` | 30 |
| `m` | 41 |

**Second child**

The second child is created after the first child has incremented `j`.

It therefore inherits the first child's current values.

It executes:

~~~c
k = k + 1;
~~~

Its variables become:

| Variable | Value |
|---|---:|
| `i` | 10 |
| `j` | 21 |
| `k` | 31 |
| `m` | 40 |

### 19.4. Final variable values

| Process | `i` | `j` | `k` | `m` |
|---|---:|---:|---:|---:|
| Parent | 11 | 20 | 30 | 40 |
| First child | 10 | 21 | 30 | 41 |
| Second child | 10 | 21 | 31 | 40 |

These values are independent because every process has its own memory space.

### 19.5. Termination order

The second child executes `exit()` after incrementing `k`.

The first child calls `wait()` and therefore waits for the second child to terminate before it can finish.

The parent also calls `wait()` and waits for the first child to terminate.

The termination order is consequently:

1. Second child.
2. First child.
3. Parent.

### 19.6. Execution order vs termination order

It is important to distinguish between the order in which instructions execute and the order in which processes terminate.

The OS scheduler determines the execution order of the processes.

Therefore, the order of the variable increments cannot generally be predicted.

However, the nested `wait()` calls impose the termination order:

**Second child → First child → Parent.**

---

## Exam Essentials

### Operating systems and processes

- An OS manages physical resources and provides services to applications.
- A process is a program in execution.
- Each process has a unique PID.
- Processes can create child processes, forming a parent-child hierarchy.
- Each process has its own memory space.

### `fork()`

- Creates a child process.
- Parent receives the child's PID.
- Child receives `0`.
- Error returns `-1`.
- Both processes continue executing after the `fork()` call.
- Parent and child have independent memory spaces.
- Never assume a fixed execution order.

### Process identification

- `getpid()` returns the current process PID.
- `getppid()` returns the parent process PID.

### `exit()`

- Terminates the current process.
- `0` indicates successful termination.
- A non-zero value indicates an error or other result.
- The exit status can be collected by the parent.

### `wait()` and `waitpid()`

- `wait()` waits for any child process.
- `waitpid()` waits for a specified child process.
- Both return the PID of the terminated child.
- Both can retrieve the child's termination status.
- If the child has already terminated, the waiting call returns immediately.

### Zombie processes

- A zombie is a terminated child whose termination status has not yet been collected.
- Calling `wait()` collects the status and cleans up the zombie.

### `exec()`

- Replaces the current program with another program.
- Does not create a new process.
- The PID remains unchanged.
- Code and data segments are replaced.
- System information and open resources such as files and sockets remain available.

### `execl()`, `argc` and `argv`

- `execl()` receives the executable path and arguments individually.
- The argument list must end with `NULL`.
- `argv[0]` is the program name.
- `argc` includes the program name.

### `fork()` + `exec()`

- `fork()` creates a child.
- The child calls `exec()` to execute another program.
- The parent continues executing its original program.
- The child keeps the same PID after `exec()`.
- The parent can use `wait()` to synchronize with the child.

### Most important exam distinctions

| Concept | Key idea |
|---|---|
| Program vs process | A program is instructions; a process is a program in execution |
| Parent vs child | The parent creates the child using `fork()` |
| `fork()` vs `exec()` | `fork()` creates a process; `exec()` replaces a program |
| `wait()` vs `waitpid()` | Any child vs a specific child |
| Execution order vs termination order | Execution is scheduled by the OS; `wait()` can impose termination dependencies |
| Parent and child variables | Each process has its own independent memory |
| Zombie process | Terminated child whose status has not yet been collected |
| `fork()` + `exec()` | Allows a parent to continue while a child executes another program |



