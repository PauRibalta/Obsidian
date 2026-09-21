## 1. Procedures and Functions

A **procedure/function** is a subprogram used to:

- Organize programs and make them more understandable.
- Reuse code.
- Perform a specific task using input parameters.
- Return computed values.

### Types of Procedures

- **Leaf procedure** → does not call other procedures/functions.
- **Nested procedure** → calls other procedures/functions.
- **Recursive procedure** → calls itself.

---

# 2. RISC-V Register Convention for Procedures

RISC-V RV64i has **32 registers of 64 bits**.

Important ABI registers:

|Register|ABI name|Use|
|---|---|---|
|`x0`|`zero`|Constant 0|
|`x1`|`ra`|Return address|
|`x2`|`sp`|Stack pointer|
|`x5-x7`|`t0-t2`|Temporary values|
|`x8`|`s0/fp`|Saved register / frame pointer|
|`x9`|`s1`|Saved register|
|`x10-x11`|`a0-a1`|Return values|
|`x12-x17`|`a2-a7`|Function arguments|
|`x18-x27`|`s2-s11`|Saved registers|
|`x28-x31`|`t3-t6`|Temporary values|

### Key convention

```
a2-a7 → input arguments
a0-a1 → return values
ra    → return address
sp    → stack pointer
fp    → frame pointer (optional)
s0-s11 → callee-saved registers
t0-t6 → caller-saved temporary registers
```

The course convention uses **a2-a7 for the first six input arguments**.

---

# 3. Basic Procedure Call Model

## Caller

The **caller** must:

1. Put input parameters in `a2-a7`.
2. Transfer control using `jal`.
3. `jal` saves the return address (`PC+4`) in `ra` (return adress).

## Callee

The **callee** must:

1. Allocate its activation area on the stack.
2. Execute the required computation.
3. Put the result in `a0`/`a1`.
4. Return control using `jr ra` or `ret`.

---

# 4. Calling and Returning

### Call

```
jal ra, LABEL
```

Equivalent effect:

```
ra ← PC + 4
PC ← LABEL
```

The assembler/linker translate `LABEL` into a PC-relative offset.

### Return

```
jr ra
```

Equivalent:

```
PC ← ra
```

or use the pseudo-instruction:

```
ret
```

Both `jr` and `ret` are pseudo-instructions in the course material.

---

# 5. Why the Stack Is Needed

Procedure calls create several problems:

- Preserving input parameters.
- Passing more than six parameters.
- Preserving registers that the caller needs after the call.
- Providing space for local variables.
- Supporting nested procedures.
- Supporting recursive procedures.

The solution is to use the **stack**.

---

# 6. Nested Procedures

Consider:

```
MAIN → A → B → C
```

Each `jal` writes a new return address into `ra`.

Therefore:

```
MAIN calls A → ra = return to MAIN
A calls B    → ra = return to A
B calls C    → ra = return to B
```

Each new `jal` **overwrites** `**ra**`.

Therefore, for nested or recursive procedures:

```
Before each nested/recursive jal:
→ save the previous ra on the stack
```

Otherwise, the previous return address is lost.

---

# 7. Activation Records / Stack Frames

In high-level languages such as C, each procedure call creates an **activation area** (stack frame) on the stack.

- The area is dynamically allocated when the procedure is called.
- The area is released when the procedure terminates.
- `main` creates the first activation area.
- Nested/recursive calls create additional stacked activation areas.
- The currently executing procedure corresponds to the top activation area.

Conceptually:

```
MAIN frame
A frame
B frame
C frame  ← currently executing
```

The stack therefore naturally supports nested and recursive execution.

![[Pasted image 20260921150611.png]]

---

# 8. Stack Organization

The RISC-V stack:

- Is a dynamic data structure.
- Uses **LIFO** (Last-In, First-Out).
- Is managed through the `sp` register.
- Uses `push` to insert data.
- Uses `pop` to remove data.
- The stack 64 bit in lenght, same as a register.

### Stack direction

The stack grows:

```
High addresses
      ↓
Lower addresses
```

The initial stack address in the course material is approximately:

```
0x3FFFFFFFF0
```

The stack grows toward lower addresses.

---

# 9. PUSH and POP

The RISC-V stack is organized in **doublewords (64 bits)**.

## PUSH

To push a 64-bit register:

```
addi sp, sp, -8
sd reg, 0(sp)
```

Steps:

1. Decrement `sp` to allocate space.
2. Store the register with `sd`.

## POP

To pop a 64-bit register:

```
ld reg, 0(sp)
addi sp, sp, 8
```

Steps:

1. Load the value with `ld`.
2. Increment `sp` to deallocate the space.

---

# 10. Example: Push Two Registers

To save `s0` and `s1`:

```
addi sp, sp, -16
sd s0, 8(sp)
sd s1, 0(sp)
```

Two registers × 8 bytes:

```
2 × 8 = 16 bytes
```

We move the stack pointer two positions down.

---

# 11. Example: Pop Two Registers

To restore them:

```
ld s1, 0(sp)
ld s0, 8(sp)
addi sp, sp, 16
```

The stack pointer is restored to its previous position by moving it two positions down.

---

# 12. Stack Frame Allocation

A procedure allocates the entire required activation area **at the beginning** of the procedure.

Example:

```
addi sp, sp, -24
```

This allocates:

```
24 bytes
```

At the end of the procedure:

```
addi sp, sp, 24
```

This deallocates the same 24 bytes.

### Important rule

```
Allocation:
sp ← sp - N

Deallocation:
sp ← sp + N
```

The same amount must be restored when returning.

---

# 13. Register-Saving Conventions

A procedure should not interfere with the caller's environment.

Registers are divided into two groups.

## Caller-Saved Registers

The **caller** is responsible for saving them if it needs their values after the call:

```
t0-t6
a0-a1
a2-a7
```

These can be freely modified by the callee.

## Callee-Saved Registers

The **callee** must preserve these if it uses them:

```
fp
ra
s0-s11
```

The callee saves them in its activation area and restores them before returning.

### Summary

```
Caller-saved:
t0-t6
a0-a7

Callee-saved:
fp
ra
s0-s11
```

  ![[Pasted image 20260921154737.png]]

---

# 14. When Must Registers Be Saved?

A register is saved **only when necessary**.

### `ra`

- A leaf procedure does not need to save `ra` if it does not execute another `jal`.
- A non-leaf procedure must save `ra` before making a nested/recursive call because `jal` overwrites it.

### `s` registers

Save the `s` registers used by the procedure because they are **callee-saved**.

If they are not used, they do not need to be saved.

---

# 15. Frame Pointer (`fp`)

The `fp` register can be used to store the beginning of the procedure's activation frame.

Benefits:

- Makes frame management easier.
- Allows fast deallocation of the activation area.
- Helps connect stacked activation records.

Use of `fp` is **optional**.

The course notes mention that GCC uses it.

Before using `fp`:

1. Save the previous `fp` on the stack.
2. Set `fp` to the appropriate position in the new activation frame.

---

# 16. Complete Procedure Call Sequence

A RISC-V procedure call consists of seven stages:

```
1. Caller prologue
2. Jump to callee ---> jal
3. Callee prologue
4. Callee body
5. Callee epilogue
6. Return to caller
7. Caller epilogue
```

---

# 17. Caller Prologue

Before calling the function, the caller:

### 1. Passes parameters

First six parameters:

```
a2 → parameter 1
a3 → parameter 2
a4 → parameter 3
a5 → parameter 4
a6 → parameter 5
a7 → parameter 6
```

If there are more than six:

```
remaining parameters → stack
```

### 2. Preserves values if necessary

If the caller needs to preserve temporary registers after the call:

```
t0-t6 → save on stack
```

If the caller needs to preserve argument/return registers:

```
a0-a7 → save on stack
```

Then:

```
jal ra, LABEL
```

---

# 18. Callee Prologue

The callee:

### 1. Allocates the activation frame

```
addi sp, sp, -N
```

where `N` is the number of required bytes.

### 2. Saves `fp` if it is used

The previous `fp` must be saved before updating it.

### 3. Saves `ra` if non-leaf

```
sd ra, offset(sp)
```

This is necessary because a nested/recursive `jal` will overwrite `ra`.

### 4. Saves required `s` registers

Only the callee-saved registers that will actually be used need to be saved.

---

# 19. Callee Body

The procedure performs its required computation.

Local variables can be:

- Stored in `s` registers.
- Stored in the activation area on the stack.

The choice depends on the variable type and how it is used.

---

# 20. Callee Epilogue

Before returning, the callee:

1. Places return value(s) in `a0`/`a1`.
2. Restores used `s` registers.
3. Restores `ra` if the function is non-leaf.
4. Restores `fp` if used.
5. Deallocates the activation area.

Example:

```
addi sp, sp, N
```

Then returns:

```
jr ra
```

or:

```
ret
```

---

# 21. Caller Epilogue

After the function returns:

- The return value is available in `a0`/`a1`.
- The caller can store the result in memory if necessary.
- Previously preserved `a0-a7` and `t0-t6` are restored if they were saved.

---

# 22. Passing Parameters

The course convention uses the first six argument registers:

```
a2 → 1st argument
a3 → 2nd argument
a4 → 3rd argument
a5 → 4th argument
a6 → 5th argument
a7 → 6th argument
```

For scalar values and pointers:

```
64-bit word
```

For an array:

```
pointer to the first element
```

If there are more than six parameters:

```
remaining parameters → stack
```

The caller is responsible for placing them there.

---

# 23. Return Values

A function returns its result in:

```
a0
```

A second return value can use:

```
a1
```

For a scalar or pointer:

```
a0 = 64-bit return value
```

For an array:

```
a0 = pointer to first element
```

The course material specifies that the first element is at index `0`.

---

# 24. Local Variables

Local variables can be handled differently depending on their type and usage.

### Scalar / Pointer

Can be stored in:

```
s0-s7
```

if:

- No memory address is required.
- Enough registers are available.

Otherwise:

```
activation area / stack
```

### Scalar accessed through a pointer

Must be stored in the activation area because it needs a memory address.

### Array / Struct

Stored in the activation area because it needs an address.

For an array:

```
array[0] → lowest memory address
```

The array is allocated on the stack starting from the lower memory addresses.

---

# 25. C → RISC-V Example

C code:

```
long x = 1;
long y = 2;
long z;

long foo(long a, long b) {
    return a + b;
}

long bar(long c, long d) {
    return foo(c, d);
}

long main() {
    z = bar(x, y);
}
```

`foo` is a **leaf function** because it does not call another function.

`bar` is a **non-leaf function** because it calls `foo`.

---

## Assembly

```
.data
X: .dword 1
Y: .dword 2
Z: .space 8

.text

FOO:
    add a0, a2, a3
    jr ra

BAR:
    addi sp, sp, -8
    sd ra, 0(sp)
    jal FOO
    ld ra, 0(sp)
    addi sp, sp, 8
    jr ra

MAIN:
    la t0, X
    ld a2, 0(t0)

    la t0, Y
    ld a3, 0(t0)

    jal BAR

    la t0, Z
    sd a0, 0(t0)
```

### Key points

`FOO`:

```
a2, a3 → input arguments
a0      → result
```

`BAR` must save `ra` because it calls `FOO`.

`MAIN`:

```
X → a2
Y → a3
```

After `BAR` returns:

```
a0 → result
```

The result is then stored in `Z`.

---

# Exam Essentials

## Procedure Basics

```
Leaf:
→ does not call another procedure

Nested:
→ calls another procedure

Recursive:
→ calls itself
```

## Calling Convention

```
Arguments:
a2-a7 → first 6 arguments

Return values:
a0-a1

Return address:
ra

Stack pointer:
sp

Frame pointer:
fp
```

## Procedure Call

```
jal ra, LABEL
```

Effect:

```
ra ← PC + 4
PC ← LABEL
```

Return:

```
jr ra
```

or:

```
ret
```

## Stack Direction

```
High addresses
      ↓
Lower addresses
```

Stack grows toward **lower addresses**.

## PUSH

```
addi sp, sp, -8
sd reg, 0(sp)
```

## POP

```
ld reg, 0(sp)
addi sp, sp, 8
```

## Stack Frame

Allocate:

```
addi sp, sp, -N
```

Deallocate:

```
addi sp, sp, N
```

Always restore `sp` to its previous value before returning.

## Register Preservation

```
Caller-saved:
t0-t6
a0-a7

Callee-saved:
fp
ra
s0-s11
```

The caller saves registers whose values it needs after the call.

The callee saves the callee-saved registers it modifies.

## `ra` Rule

The most important nested-call rule:

```
jal overwrites ra
```

Therefore:

```
Non-leaf function
→ save ra before nested/recursive jal
→ restore ra before returning
```

## Procedure Call Phases

```
1. Caller prologue
2. jal → callee
3. Callee prologue
4. Callee body
5. Callee epilogue
6. jr ra / ret
7. Caller epilogue
```

## Local Variables

```
Scalar/pointer:
→ s0-s7 if no memory address is needed

Scalar accessed by pointer:
→ stack

Array/struct:
→ stack
```

For arrays:

```
array[0] → lowest memory address
```

## Must-Know Concepts

- Difference between **caller-saved** and **callee-saved** registers.
- Why `ra` must be saved in nested/non-leaf procedures.
- How `sp` changes during push/pop and frame allocation.
- How arguments and return values are passed.
- Difference between leaf and non-leaf procedures.
- Purpose of `fp`.
- Structure of a stack frame.
- Complete caller/callee call sequence.
- Translation of simple C functions into RISC-V.