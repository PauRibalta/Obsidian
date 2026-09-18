## 1. Abstraction Levels

From higher to lower abstraction:

1. **High-level language** → productivity and portability.
2. **Assembly language** → symbolic/textual representation of instructions.
3. **Machine language** → binary encoding of instructions and data.
4. **Hardware** → physical implementation.

As abstraction decreases, concepts become more concrete and more implementation details are exposed.

---

## 2. Assembly vs Machine Language

**Assembly language**

- Symbolic representation of machine instructions.
- Uses symbolic names for instructions, registers, data and memory references.
- Instructions correspond approximately one-to-one with machine instructions.

**Machine language**

- Binary representation directly interpreted by the processor.

The **assembler** translates assembly into machine code.

The assembler:

- The assembler translates instruction mnemonics (machine instructions) into machine code.
- Resolves symbolic references.
- Allocates memory for variables.

The **linker** is required when references depend on other modules or relocation.

---

## 3. Translation Process

The complete process is:

```
C source
   ↓
Compilation
   ↓
Assembly
   ↓
Linking
   ↓
Loading
   ↓
Execution
```

- **Compilation** → C → assembly.
- **Assembly** → assembly → machine code.
- **Linking** → combines modules/libraries and resolves references.
- **Loading** → loads executable into memory.
- **Execution** → CPU executes instructions.

---

## 4. CPU and Memory

### CPU

Main components:

- **Control Unit** → fetches and decodes instructions.
- **ALU (Arithmetic Logic Unit)** → performs arithmetic and logical operations.
- **Register File** → fast internal CPU storage.
- **PC (Program Counter)** → identifies the next instruction.
- **IR (Instruction Register)** → contains the current instruction.

### Memory

Memory contains:

- Instructions to execute.
- Data used by the program.

### System Buses

- **Address bus** → identifies memory locations.
- **Data/Instruction bus** → transfers data and instructions.
- **Control bus** → carries control signals such as read/write.

---

## 5. Instruction Execution

Main phases:

1. **Instruction fetch** → instruction loaded into IR.
2. **Decode** → Control Unit determines the instruction.
3. **Operand fetch** → required operands are obtained.
4. **Execution** → ALU performs the operation.
5. **Result storage** → result stored in a register or memory.

---

## 6. Memory Organization

Memory is **byte-addressable**.

- 1 byte = 8 bits.
- 1 word = 32 bits = 4 bytes.
- 1 double word = 64 bits = 8 bytes.

For example, a 32-byte memory requires:

 2 ^5=32 

different byte addresses → **5 address bits**.

### Little Endian

RISC-V uses **Little Endian**.

The address of a word corresponds to the address of its **least significant byte**.

---

## 7. ISA

**ISA (Instruction Set Architecture)** describes the machine-language interface of a processor.

It defines:

- Register organization.
- Memory model.
- Instruction set.
- Instruction formats and sizes.
- Operand addressing modes.
- Data types and sizes.
- Operational modes.

Two processors can have the same ISA while having different hardware implementations.

---

## 8. RISC-V

RISC-V belongs to the **RISC (Reduced Instruction Set Computer)** family.

Main RISC objectives:

- Simplify instructions.
- Facilitate hardware and compiler design.
- Reduce instruction execution time.
- Reduce chip complexity/cost.

RISC-V is an **Open Standard Instruction Set Architecture**.

The course uses **RV64i**.

---

## 9. RISC-V Registers

RISC-V has:

32 general-purpose registers  

Each register is:
64 bits  

Registers are named:

```
x0, x1, ..., x31
```

Since there are 32 registers:

log_2(32)=5 bits  


are required to encode a register.

### Important ABI names

| Register  | ABI name | Use                                 |
| --------- | -------- | ----------------------------------- |
| `x0`      | `zero`   | Constant 0                          |
| `x1`      | `ra`     | Return address                      |
| `x2`      | `sp`     | Stack pointer                       |
| `x3`      | `gp`     | Global pointer — not used in course |
| `x4`      | `tp`     | Thread pointer — not used in course |
| `x5-x7`   | `t0-t2`  | Temporary values                    |
| `x8`      | `s0/fp`  | Local variable / frame pointer      |
| `x9`      | `s1`     | Local variable                      |
| `x10-x11` | `a0-a1`  | Return values                       |
| `x12-x17` | `a2-a7`  | Function arguments                  |
| `x18-x27` | `s2-s11` | Local variables                     |
| `x28-x31` | `t3-t6`  | Temporary values                    |

General-purpose registers contain integer values.

Floating-point values use separate floating-point registers (`f0`, `f1`, ...).

---

## 10. Special Registers

Three additional 64-bit registers are mentioned but are **not directly referenceable in ISA instructions**:

- `pc` → Program Counter.
- `hi` → multiplication/division register.
- `lo` → multiplication/division register.

---

## Exam Essentials

- Assembly = symbolic representation; machine language = binary.
- **Assembler** translates assembly → machine code.
- **Linker** resolves references between modules.
- Translation: **compile → assemble → link → load → execute**.
- CPU: **Control Unit + ALU + Register File + PC + IR**.
- RISC-V RV64i → **32 × 64-bit general registers**.
- Register identifiers require **5 bits**.
- x0 = zero, x1 = ra, x2 = sp.
- Memory is **byte-addressable**.
- Word = **32 bits / 4 bytes**.
- Double word = **64 bits / 8 bytes**.
- RISC-V uses **Little Endian**.
- ISA defines instructions, registers, memory model, formats and addressing modes.