## 1. Translation Model

The course presents a possible translation from:

```text
C → RISC-V RV64i assembly → machine code
```

The model is based on:

- **RISC-V RV64i**
    
- **GCC ABI**
    
- Linux C conventions
    

The goal is to establish conventions for:

- Program segmentation.
    
- Variable declaration.
    
- Reading/writing variables.
    
- Translating control structures.
    

The translation shown is not necessarily the most efficient one.

---

## 2. Translation Stages

### Compilation

C source is translated into symbolic RISC-V assembly.

The result is still **symbolic**, so it cannot be executed directly.

### Assembly

The assembler:

- Expands pseudo-instructions.
    
- Converts symbolic instructions into binary.
    
- Resolves labels.
    
- Resolves memory addresses.
    
- Resolves offsets.
    

### Linking

The linker:

- Combines multiple object modules.
    
- Links libraries.
    
- Generates executable machine code.
    

---

## 3. Program Memory

A running program is divided into three main segments:

### Text / Code

Contains:

- `main`
    
- User functions
    
- Machine instructions
    

### Data

Contains:

- Global variables.
    
- Static variables.
    
- Dynamic data.
    

### Stack

Contains function activation records, including:

- Addresses.
    
- Parameters.
    
- Saved registers.
    
- Local variables.
    

The **stack is created dynamically by the operating system** when the process starts.

---

## 4. RISC-V Memory Layout

The course model uses:

- Static data starting at approximately:
    

0x0000000010000000

- Text starting at approximately:
    

0x0000000000400000

- Stack starting around:
    

0x0000003FFFFFFFF0

and growing toward **lower addresses**.

- Dynamic data grows toward **higher addresses**.
    

Conceptually:

```text
High addresses
┌────────────────────┐
│       Stack        │ ↓ grows downward
├────────────────────┤
│   Dynamic data     │ ↑ grows upward
├────────────────────┤
│   Static / Data    │
├────────────────────┤
│   Text / Code      │
└────────────────────┘
Low addresses
```

---

## 5. `.data` and `.text`

### `.data`

Declares the data segment.

```asm
.data
```

### `.text`

Declares the code/text segment.

```asm
.text
```

The stack does not need to be explicitly declared.

---

## 6. Variables and Memory

C variables are typed and therefore have a specific size.

In RISC-V:

- **Registers** → internal CPU storage.
    
- **RAM** → external memory.
    
- Memory is **byte-addressable**.
    

A variable stored in memory corresponds to:

- A memory address.
    
- The value stored at that address.
    

### Important Rule

Arithmetic/logic instructions operate on **registers**, not directly on memory variables.

For a memory variable:

1. **Load** it into a register.
    
2. Perform the operation in registers.
    
3. **Store** the result back to memory.
    

```text
Memory → Load → Register
                 ↓
             Operation
                 ↓
Register → Store → Memory
```

If a variable is already in a register, the load/store steps are unnecessary unless the value must be written to memory.

---

## 7. Variable Sizes

The course uses:

|C type|Size|RISC-V notation|
|---|--:|---|
|`char`|1 byte|`b`|
|`short int`|2 bytes|`h`|
|`int`|4 bytes|`w`|
|`long int`|8 bytes on Linux|`d` / `dword`|
|`long long int`|8 bytes|`d` / `dword`|

The course uses:

```c
typedef long long int LONG;
```

Therefore:

LONG=64 bits=8 bytes

Also:

sizeof(array)=∑sizeof(elements)
sizeof(struct)=∑sizeof(fields)

Floating-point types are not considered in the course.

---

## 8. Variables

- **Global variables:** allocated in memory at a fixed (absolute) address
    
- **Local variables and parameters:** allocated in processor registers or in the stack frame (activation record)
    
- **Dynamic memory variables:** allocated on the heap (not considered here)

### Global Variables
Global variables are allocated in the static data segment.

The symbolic address normally corresponds to the variable name written in uppercase.

Example:

```c
LONG a, b, c;
```

becomes:

```asm
A: .dword
B: .dword
C: .dword
```

Allocation follows declaration order.

For 64-bit variables:

```text
A → base
B → base + 8
C → base + 16
```

---

## 9. Memory Alignment

Variables are aligned according to their size:

|Type|Alignment|
|---|---|
|Byte (8 bit)|Any address|
|Half-word (16 bit)|Multiple of 2|
|Word (32 bit)|Multiple of 4|
|Double word (64 bit)|Multiple of 8|

### `.align`

```asm
.align 0 → byte alignment
```


```asm
.align 1 → 2-byte alignment
```


```asm
.align 2 → 4-byte alignment
```
	

```asm
.align 3 → 8-byte alignment
```


---

## 10. Main Assembler Directives

|Directive|Purpose|
|---|---|
|`.data`|Data segment|
|`.text`|Code/text segment|
|`.byte`|Allocate 8-bit values|
|`.half`|Allocate 16-bit values|
|`.word`|Allocate 32-bit values|
|`.dword`|Allocate 64-bit values|
|`.space`|Reserve bytes|
|`.zero`|Reserve zero-initialized space|
|`.align`|Set alignment|
|`.eqv`|Define symbolic constant|
|`.globl`|Export symbol|
|`.ascii`|String without null terminator|
|`.asciiz`|Null-terminated string|
|`.macro`|Define macro|
|`.end_macro`|End macro|
|`.rodata`|Read-only data|

---

## 11. `.space` vs `.zero`

### `.space`

```asm
.space n
```

Allocates `n` bytes without initializing them.

### `.zero`

```asm
.zero n
```

Allocates `n` bytes for zero-initialized data.

The course associates `.zero` with the **BSS** area for uninitialized static variables.

---

## 12. `.eqv` and `.globl`

### `.eqv`

```asm
.eqv CONSTANT, value
```

Defines a symbolic constant without allocating memory.

Similar to:

```c
#define CONSTANT value
```

### `.globl`

```asm
.globl MAIN
```

Exports a symbol so it can be referenced by other object modules.

---

## 13. Global Variable Example

C:

```c
LONG a;
a = 1;
```

Assembly:

```asm
.data
A: .space 8

.text
li t0, 1
la t1, A
sd t0, (t1)
```

Process:

1. Reserve 8 bytes for `A`.
    
2. Load constant `1` into `t0`.
    
3. Load address of `A` into `t1`.
    
4. Store 64-bit value into `A`.
    

---

## 14. Loading and Storing Variables

Example:

```c
LONG a;
LONG b = 1;
a = b;
```

Assembly concept:

```asm
.data
A: .space 8
B: .dword 1

.text
la t0, B
ld t1, (t0)

la t0, A
sd t1, (t0)
```

Remember:

```text
la → address
ld → value from memory
sd → value to memory
```

---

## 15. Arithmetic with Global Variables

Example:

```c
LONG a = 1;
LONG b = 2;
LONG c;

c = a + b;
```

Typical translation:

```asm
.data
A: .dword 1
B: .dword 2
C: .space 8

.text
la t0, A
ld t1, (t0)

la t0, B
ld t2, (t0)

add t3, t1, t2

la t0, C
sd t3, (t0)
```

General pattern:

```text
load a
load b
operate
store result
```

---

## 16. Pointers

A pointer contains a memory address.

Example:

```c
LONG *p;
LONG a;

p = &a;
*p = 1;
```

Conceptually:

```asm
la t0, A
la t1, P
sd t0, (t1)
```

This performs:

p=&ap=\&a

Then:

```asm
li t0, 1
la t1, P
ld t2, (t1)
sd t0, (t2)
```

This performs:

∗p=1*p=1

A pointer is always **64 bits / 8 bytes**, independently of the type of object it points to.

---

## 17. Arrays

For an array of 64-bit `LONG` elements:

sizeof(element)=8 bytessizeof(element)=8\text{ bytes}

Therefore:

Address(A[i])=Base(A)+8iAddress(A[i])=Base(A)+8i

### Constant Index

For:

```c
A[3]
```

the offset is:

3×8=243\times8=24

Therefore:

```asm
ld s1, 24(s2)
```

---

## 18. Generic Array Access

For:

```c
a = A[i];
```

Assume:

- `i` → `s3`
    
- Base address of `A` → `s2`
    
- `a` → `s1`
    

Assembly:

```asm
slli t1, s3, 3
add  t2, s2, t1
ld   s1, 0(t2)
```

Explanation:

i×8

then:

Base+i×8

then load the element.

---

## 19. Array Example

C:

```c
A[12] = h + A[8];
```

If:

- `h` → `s4`
    
- Base address of `A` → `s2`
    

Then:

8×8=648\times8=64 12×8=9612\times8=96

Assembly:

```asm
ld t0, 64(s2)
add t0, s4, t0
sd t0, 96(s2)
```

---

## 20. Control Structures

Control flow is implemented using **conditional branches and jumps**.

Normally:

PC=PC+4PC=PC+4

A branch/jump modifies the normal sequence.

---

## 21. `if ... else`

C:

```c
if (a == 5) {
    // then
} else {
    // else
}
```

Typical structure:

```asm
IF:
    ...
    bne t1, t2, ELSE

THEN:
    ...
    j ENDIF

ELSE:
    ...

ENDIF:
    ...
```

The branch skips the `then` part when the condition is false.

---

## 22. `while`

C:

```c
while (a == 5) {
    // body
}
```

Typical structure:

```asm
WHILE:
    ...
    bne t1, t2, ENDWHILE

    ... body ...

    j WHILE

ENDWHILE:
    ...
```

The condition is checked **before** executing the body.

---

## 23. `do ... while`

C:

```c
do {
    // body
} while (a == 5);
```

Typical structure:

```asm
DO:
    ... body ...

    ...
    beq t1, t2, DO

ENDDO:
    ...
```

The body is executed **before** the condition is checked.

Therefore it executes at least once.

---

## 24. `for`

C:

```c
for (a = 2; a <= 5; a++) {
    // body
}
```

Typical structure:

```asm
li t0, 2
la t1, A
sd t0, (t1)

FOR:
    la t0, A
    ld t1, (t0)
    li t2, 5

    bgt t1, t2, ENDFOR

    ... body ...

    la t0, A
    ld t1, (t0)
    addi t1, t1, 1
    sd t1, (t0)

    j FOR

ENDFOR:
    ...
```

Structure:

```text
initialization
      ↓
condition
      ↓
body
      ↓
increment
      ↓
back to condition
```

---

## 25. `li` Expansion

For a 32-bit constant:

```asm
li t0, 0x12345678
```

can be expanded conceptually as:

```asm
lui t0, %hi(constant)
addi t0, t0, %lo(constant)
```

![[Pasted image 20260918123427.png]]

---

## 26. `la` Expansion

For a symbolic address:

```asm
la t0, A
```

the course uses PC-relative construction:

```asm
auipc t0, %pcrel_hi(A)
addi  t0, t0, %pcrel_lo(A)
```

![[Pasted image 20260918123442.png]]

---

## Exam Essentials

- Course model → **RISC-V RV64i + GCC ABI**.
    
- Main memory segments → **text, data, stack**.
    
- `.data` → data segment.
    
- `.text` → code segment.
    
- Static data starts around `0x10000000`.
    
- Text starts around `0x400000`.
    
- Stack grows toward **lower addresses**.
    
- Dynamic data grows toward **higher addresses**.
    
- `LONG` = **64 bits = 8 bytes**.
    
- Pointer = **64 bits = 8 bytes**.
    
- Byte = 1 B; half = 2 B; word = 4 B; dword = 8 B.
    
- `.align 0/1/2/3` → 1/2/4/8-byte alignment.
    
- Arithmetic operates on **registers**, not directly on memory.
    
- Memory variable → **load → operate → store**.
    
- Array of LONG:
    

Address(A[i])=Base+8iAddress(A[i])=Base+8i

- `slli index, index, 3` → multiply index by 8.
    
- `la` → address.
    
- `ld` → load value.
    
- `sd` → store value.
    
- `if`, `while`, `do-while`, `for` → branches + jumps.
    
- `li` → constant construction using `lui + addi`.
    
- `la` → PC-relative address construction using `auipc + addi`.
    
- HI/LO correction uses bit 11 because the lower immediate is signed.