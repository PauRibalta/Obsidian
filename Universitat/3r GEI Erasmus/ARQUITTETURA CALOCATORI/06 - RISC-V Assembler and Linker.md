## 1. Code Translation Process

Source code is translated through several stages:

```text
C source file (.c)
        ↓ compiler
Assembly file (.s)
        ↓ assembler
Object file (.o)
        ↓ linker + libraries
Executable file
```

- **Compiler:** translates C source code into symbolic assembly.
- **Assembler:** translates symbolic assembly into binary machine code → **object code**.
- **Linker:** combines object files and libraries into a single executable program.

Object code is **not yet executable** because some references may remain unresolved:

- Pseudo-instructions
- Variable accesses
- Labels used in jumps
- Function calls

---

# 2. Assembler

The assembler processes the assembly source **line by line** and translates symbolic instructions into binary machine code.

It translates:

- Instruction opcodes → binary opcode fields
- Register references → binary register addresses
- Symbolic references → corresponding addresses when possible

The assembler also generates:

- **Symbol table**
- **Relocation table**

The executable is generated later by the **linker** from multiple modules, such as `main`, functions and standard libraries.

---

# 3. Pseudo-Instructions

Pseudo-instructions extend the RISC-V assembly language beyond the native instruction set.

The assembler expands them into sequences of native instructions.

### `move`

```asm
move rd, rs1
```

expands to:

```asm
addi rd, rs1, 0
```

### `li`

For a 32-bit constant:

```asm
li rd, cost32
```

expands to:

```asm
lui  rd, %hi(cost32)
addi rd, rd, %lo(cost32)
```

The assembler therefore determines the actual native instructions before the final machine code is produced.

---

# 4. Object Files

For each source module, the assembler generates a corresponding object module.

The object file contains information such as:

- Text segment
- Static data segment
- Symbol table
- Relocation information
- Object code

The object code may still contain **unresolved references**.

### Why?

A symbol may:

1. Be defined in another module → **external symbol**
2. Depend on where the module is placed in memory → **relocatable symbol**

These references are handled later by the linker.

---

# 5. First Pass of the Assembler

The first assembler pass:

1. Expands pseudo-instructions.
2. Builds the module's symbol table.

The symbol table contains labels for:

- Instruction destinations
- Variables in the `.data` segment

Each entry contains:

```text
<symbol, relocatable address, segment>
```

The addresses are initially relative to the beginning of the corresponding segment.

The source uses:

```text
.text base = 0x0040 0000
.data base = 0x1000 0000
```

during the final memory layout, while the assembler initially works with relocatable addresses starting from `0`.

Example:

```text
SYMBOL      ADDRESS             SEGMENT
LABEL_IND   relocatable         Text (T)
LABEL_VAR   relocatable         Data (D)
```

---

# 6. Static Data Addressing

Access to static variables and arrays is performed using `la` followed by a load/store instruction.

Example:

```asm
la t0, Y
ld t1, 0(t0)
```

`la` loads the **address** of the variable.

The pseudo-instruction:

```asm
la rd, VAR
```

expands to:

```asm
auipc rd, %pcrel_hi(VAR)
addi  rd, rd, %pcrel_lo(VAR)
```

The linker later calculates the PC-relative displacement:

```text
%pcrel(VAR) = VAR - PC
```

`%pcrel_hi` and `%pcrel_lo` contain the upper and lower components of this displacement.

---

# 7. Second Pass of the Assembler

The second pass performs the actual translation.

It:

- Uses the symbol table.
- Generates machine code.
- Generates the **relocation table**.

If a symbolic reference cannot yet be resolved, the instruction is generated with the corresponding value temporarily set to `0`.

Examples:

### `.data` scalar

The value is set to `0` by convention.

### Array address

`la` is expanded into:

```asm
auipc
addi
```

with immediate fields initially set to `0`.

### 32-bit constant

`li` is expanded into:

```asm
lui
addi
```

with immediate fields initially set to `0` when relocation is required.

### External branch/jump symbol

The symbolic displacement is initially set to `0`.

The linker will later calculate the final values.

---

# 8. Relocation Table

The relocation table identifies references that still need to be resolved.

An entry can be represented as:

```text
<relocatable instruction address, instruction, symbol>
```

For example:

```text
4  auipc  %pcrel_hi(Y)
8  addi   %pcrel_lo(Y)
```

The symbol `Y` must later be replaced with its final address.

Another example:

```text
0xC  beq  %pcrel(E) / 2
0x10 jal  %pcrel(B) / 2
```

Here `E` and `B` must be resolved by the linker.

---

# 9. Local vs External Symbols

Not every symbolic reference needs the linker.

If a symbol is local and its position can already be determined, the assembler can resolve it.

Example:

```asm
B: bne a2, zero, E
...
E:
```

Since `E` is local to the same module, the branch displacement can be calculated during assembly.

For B-format branches:

```text
%pcrel(E) / 2
```

The document's example:

```text
E = 0x10
PC = 0x00

(0x10 - 0x00) / 2 = 0x08
```

Thus the branch is encoded with `0x8`, corresponding to a jump forward of 4 instructions.

---

# 10. Example Object Module

Source:

```asm
.eqv CONST, 0x12345678

.data
Y: .dword 0

.text
B:
    bne a2, zero, E
    la t0, Y
    sd a1, 0(t0)
E:
    li t1, CONST
```

After pseudo-instruction expansion:

```asm
B:
    bne a2, zero, E
    auipc t0, %pcrel_hi(Y)
    addi t0, t0, %pcrel_lo(Y)
    sd a1, 0(t0)
E:
    lui t1, %hi(CONST)
    addi t1, t1, %lo(CONST)
```

The relocation table contains:

```text
4   auipc  %pcrel_hi(Y)
8   addi   %pcrel_lo(Y)
```

The branch to `E` is already resolved because `E` is local.

---

# 11. Example with Multiple Modules

Suppose there are two modules:

```text
MAIN
B
```

`MAIN` contains:

```asm
.data
X: .dword 128

.text
MAIN:
    la t0, X
    ld a2, 0(t0)
    beq a2, zero, E
    jal ra, B
```

`B` contains:

```asm
.data
Y: .dword 0

.text
B:
    bne a2, zero, E
    la t0, Y
    sd a1, 0(t0)
E:
    li t1, CONST
```

When `MAIN` is assembled separately:

- `X` is known locally.
- `E` is external to `MAIN`.
- `B` is external to `MAIN`.

Therefore:

```text
Relocation table of MAIN:

0   auipc  %pcrel_hi(X)
4   addi   %pcrel_lo(X)
C   beq    %pcrel(E) / 2
10  jal    %pcrel(B) / 2
```

---

# 12. RISC-V Virtual Memory Layout

For RV64:

- Addresses are 64-bit.
- They are represented using **16 hexadecimal digits**.

The document gives the following relevant areas:

```text
.text  → starts around 0x0040 0000
.data  → starts around 0x1000 0000
```

The upper part of the virtual address space is reserved for the operating system.

Static data grows toward higher addresses.

---

# 13. Linker

The **linker** combines multiple object modules into one executable program.

Its main tasks are:

1. Determine where each module's text and data sections will be placed.
2. Create a global symbol table with relocated addresses.
3. Resolve symbolic references using the relocation tables.

The linker therefore creates a **single virtual address space** for the complete program.

---

# 14. Module Placement

The assembler initially treats each module's text and data segments as starting at address `0`.

The linker cannot place every module at the same address.

It therefore places modules sequentially.

Example:

```text
Text MAIN:   base = 0x0040 0000
Text B:      base = 0x0040 0014

Data MAIN:   base = 0x1000 0000
Data B:      base = 0x1000 0008
```

The base address of a module depends on the size of the preceding module.

---

# 15. Global Symbol Table

The linker combines the symbol tables of all modules.

Each symbol's final address is:

```text
IND_FINALE = IND_INIZIALE + IND_BASE_MODULO
```

Example:

```text
Symbol   Initial   Module Base   Final

MAIN     0         0x00400000    0x00400000
X        0         0x10000000    0x10000000
B        0         0x00400014    0x00400014
E        10        0x00400014    0x00400024
Y        0         0x10000008    0x10000008
```

The final addresses are the addresses used by the executable.

---

# 16. Resolving Relocations

For a relocatable instruction:

```text
L1 = IND + BASE_M
```

where:

- `IND` = instruction's relocatable address
- `BASE_M` = base address of module `M`
- `L1` = final address of the instruction

The PC-relative displacement is:

```text
%pcrel(S) = L1 - PC
```

where `S` is the referenced symbol.

---

# 17. Relocation Rules by Instruction Format

The linker applies different rules depending on the instruction format.

### J-format

For a jump:

```text
%pcrel(S) / 2
```

is inserted into the 20-bit immediate.

```text
(L1 - PC) / 2
```

### B-format

For a branch:

```text
%pcrel(S) / 2
```

is inserted into the 12-bit immediate.

```text
(L1 - PC) / 2
```

### U-format

For `auipc` generated by `la`:

```text
%pcrel_hi(S)
```

For `lui` generated by `li`:

```text
%hi(S)
```

### I-format

For `addi` generated by `la`:

```text
%pcrel_lo(S)
```

For `addi` generated by `li`:

```text
%lo(S)
```

---

# 18. Worked Relocation Example

Final symbol addresses:

```text
MAIN = 0x0040 0000
X    = 0x1000 0000
B    = 0x0040 0014
E    = 0x0040 0024
Y    = 0x1000 0008
```

Relevant PCs:

```text
MAIN:
0x0040 0000 → auipc for X
0x0040 0004 → addi for X
0x0040 000C → beq to E
0x0040 0010 → jal to B

B:
0x0040 0018 → auipc for Y
0x0040 001C → addi for Y
```

Calculate:

```text
%pcrel(X) = 0x1000 0000 - 0x0040 0000
          = 0x0FC0 0000

%pcrel(E) = 0x0040 0024 - 0x0040 000C
          = 0x0000 0018

%pcrel(B) = 0x0040 0014 - 0x0040 0010
          = 0x0000 0004

%pcrel(Y) = 0x1000 0008 - 0x0040 0018
          = 0x0FBF FFF0
```

Then:

```text
%pcrel_hi(X) = 0x0FC00
%pcrel_lo(X) = 0x000

%pcrel(E) / 2 = 0x00C
%pcrel(B) / 2 = 0x0002

%pcrel_hi(Y) = 0x0FC00
%pcrel_lo(Y) = 0xFF0
```

Important: the high part uses the appropriate correction from the low part when necessary.

---

# 19. Final Machine Code

After linking, the instructions contain complete numerical fields.

Example:

```text
0x0040 0000  auipc t0, 0xFC00
0x0040 0004  addi  t0, t0, 0x000
0x0040 0008  ld    a2, 0(t0)
0x0040 000C  beq   a2, zero, ...
0x0040 0010  jal   ra, ...
```

The document gives examples of resulting machine-code values such as:

```text
auipc t0, ... → 0x0FC00297
addi  t0, ... → 0x00028293
addi  t0, ... → 0xFF028293
addi  t1, ... → 0x67830313
```

The important point is that after linking, all required symbolic references have been resolved.

---

# 20. Object File vs Executable File

## Object file

Contains:

- Header
- Text segment
- Static data segment
- Relocation information
- Symbol table
- Optional debug information

The text/data may still contain unresolved references.

## Executable file

Similar structure, but represents the **complete program** after linking.

It contains:

- Header
- Complete executable code
- Initial values for static data
- Optional global symbol table

All instruction fields are numerically resolved.

The header also contains the program's **initial execution address**, corresponding to `MAIN`.

---

# 21. Runtime Loading and Execution

In UNIX/Linux systems, the operating system kernel loads the executable into main memory.

Main steps:

1. Read the executable header.
2. Determine text/data sizes and initial execution address.
3. Create a new virtual address space.
4. Load text and initial static data.
5. Load program arguments onto the stack.
6. Initialize the architecture registers.
7. Initialize `sp` with the address of the first free stack cell.
8. Execute the startup procedure.
9. The startup procedure copies arguments from the stack to registers.
10. It calls `main`.
11. When `main` terminates, the startup procedure finishes the program using `exit`.

---

# Exam Essentials

- **Compiler:** C → symbolic assembly.
    
- **Assembler:** assembly → machine code/object file.
    
- **Linker:** multiple object files + libraries → executable.
    
- Object code is not necessarily executable because it can contain **unresolved symbolic references**.
    
- Pseudo-instructions are expanded by the assembler.
    
- `move`:
    
    ```text
    move rd, rs1 → addi rd, rs1, 0
    ```
    
- `li`:
    
    ```text
    li rd, cost32
    → lui rd, %hi(cost32)
    → addi rd, rd, %lo(cost32)
    ```
    
- `la`:
    
    ```text
    la rd, VAR
    → auipc rd, %pcrel_hi(VAR)
    → addi rd, rd, %pcrel_lo(VAR)
    ```
    
- First assembler pass:
    
    - Expand pseudo-instructions.
        
    - Build symbol table.
        
- Second assembler pass:
    
    - Generate machine code.
        
    - Generate relocation table.
        
- Local resolvable symbols can be resolved by the assembler.
    
- External/relocatable symbols are resolved by the linker.
    
- Final symbol address:
    
    ```text
    IND_FINALE = IND_INIZIALE + IND_BASE_MODULO
    ```
    
- PC-relative displacement:
    
    ```text
    %pcrel(S) = L1 - PC
    ```
    
- J/B relocation:
    
    ```text
    %pcrel(S) / 2
    ```
    
- U-format:
    
    ```text
    auipc → %pcrel_hi(S)
    lui   → %hi(S)
    ```
    
- I-format:
    
    ```text
    addi (la) → %pcrel_lo(S)
    addi (li) → %lo(S)
    ```
    
- Linker responsibilities:
    
    1. Place modules in memory.
        
    2. Build the global symbol table.
        
    3. Resolve relocation references.
        
- RV64 addresses are 64-bit / 16 hexadecimal digits.
    
- Typical bases:
    
    ```text
    .text → 0x0040 0000
    .data → 0x1000 0000
    ```
    
- Executable contains fully resolved machine instructions.
    
- At runtime, the OS loads the executable, initializes memory/registers/stack, and starts execution at `MAIN`.