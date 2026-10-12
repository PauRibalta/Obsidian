## 1. Main Characteristics of RISC-V

**RISC-V** (_Reduced Instruction Set Computer_) has three main characteristics:

- **RISC architecture:** simple instructions, a reduced number of instruction formats, and simple operations designed for execution in a clock cycle.

- **Load/store architecture:** ALU operands come only from general-purpose registers, not directly from memory.
    
    - `ld`: loads data from memory into a register.
    - `sd`: stores data from a register into memory.
- **Pipeline architecture:** improves performance by overlapping the execution stages of different instructions. This is introduced in a later lecture.

### 1.1 Reduced Instruction Set in This Implementation

The simplified processor supports:

- **R-type:** `add`, `sub`, `and`, `or`.
- **I-type:** `ld` and arithmetic-logical instructions with an immediate.
- **S-type:** `sd`.
- **B-type:** conditional branches `beq` and `bne`.

U-type and J-type instructions are not included in this implementation.

### 1.2 Instruction Opcodes

|Instruction type|Instructions|Opcode|
|---|---|---|
|R|`add`, `sub`, `and`, `or`|`0110011`|
|I|`ld`|`0000011`|
|S|`sd`|`0100011`|
|B|`beq`, `bne`|`1100011`|

## 2. Instruction Formats and Immediates

RISC-V instructions are 32 bits long. The processor decodes their fields to select registers, determine the operation, and construct immediate values.

### 2.1 R-Type Format

```
31        25 24    20 19    15 14   12 11     7 6       0
+-----------+--------+--------+-------+--------+---------+
|  funct7   |  rs2   |  rs1   | funct3|   rd   | opcode  |
+-----------+--------+--------+-------+--------+---------+
    7 bits   5 bits   5 bits  3 bits  5 bits    7 bits
```

- `rs1`, `rs2`: source registers.
- `rd`: destination register.
- `funct3` and `funct7`: specify the ALU operation together with the opcode.
- `opcode`: identifies the instruction class.

### 2.2 I-Type Format

```
31                  20 19    15 14   12 11     7 6       0
+---------------------+--------+-------+--------+---------+
|      imm[11:0]      |  rs1   | funct3|   rd   | opcode  |
+---------------------+--------+-------+--------+---------+
```

Used for loads and arithmetic-logical operations with an immediate.

### 2.3 S-Type Format

```
31        25 24    20 19    15 14   12 11      7 6       0
+-----------+--------+--------+-------+----------+---------+
| imm[11:5] |  rs2   |  rs1   | funct3| imm[4:0] | opcode  |
+-----------+--------+--------+-------+----------+---------+
```

Used for store instructions. The immediate is split across two fields.

### 2.4 B-Type Format

The branch immediate is also split across several instruction fields. It represents an offset in halfwords, so its reconstructed byte offset is shifted left by one bit.

### 2.5 Sign Extension

The sign-extension unit reconstructs an immediate from the instruction fields and extends it to 64 bits, preserving its sign.

For the reduced instruction set:

- **I-type** `**ld**`**:** 12-bit signed byte offset added to the base register.
- **S-type** `**sd**`**:** 12-bit signed byte offset added to the base register.
- **B-type** `**beq**`**/**`**bne**`**:** signed branch offset reconstructed from the instruction, then shifted left by one bit to convert from halfword units to byte units.

For loads and stores:

```
offset_ext = sign_extend(imm12, 64)
address    = R[rs1] + offset_ext
```

For conditional branches:

```
offset_ext = sign_extend(branch_imm, 64) << 1
BTA        = PC + offset_ext
```

`BTA` is the _Branch Target Address_.

## 3. Basic Processor Blocks

### 3.1 Program Counter and Instruction Fetch

The **Program Counter (PC)** stores the address of the current instruction.

The fetch stage:

1. Sends the PC to Instruction Memory.
2. Reads the 32-bit instruction.
3. Calculates `PC + 4`, the address of the next sequential instruction.
4. Decode  the intruction and select the register adress to read in the Register file.

```
Instruction = InstructionMemory[PC]
Next sequential address = PC + 4
```

The processor uses an adder to calculate `PC + 4`.

### 3.2 Register File

The Register File (RF) contains:

- 32 registers, each 64 bits wide.
- Two independent read ports, each returning a 64-bit operand.
- One write port for a 64-bit result.
- 5-bit register addresses.

The two source registers can be read during a cycle. Writing the destination register is controlled by `RegWrite`.

### 3.3 ALU

The 64-bit Arithmetic Logic Unit receives two 64-bit operands and produces a 64-bit result.

Its operation is selected by the control signal `OP_ALU`. Depending on the instruction, the ALU performs arithmetic or logical operations, calculates memory addresses, or compares register values for branches.

![[Pasted image 20261012152851.png|674]]
### 3.4 Additional Blocks

The processor also needs:

- **Sign-extension unit:** constructs 64-bit immediates and offsets.
- **Data Memory:** used for loads and stores.
- **Adders:** calculate `PC + 4` and branch target addresses.
- **Multiplexers (MUXes):** select between alternative inputs or results.
- **Control unit:** generates control signals based on the instruction.

## 4. Execution of R-Type Instructions

Example syntax:

```
add rd, rs1, rs2
sub rd, rs1, rs2
and rd, rs1, rs2
or  rd, rs1, rs2
```

These instructions use two register operands and write the ALU result to a destination register.

### Execution in Four Steps

1. **Fetch:** read the instruction and calculate `PC + 4`.
    
2. **Register read:** decode `rs1` from bits `[19:15]` and `rs2` from bits `[24:20]`.
    ![[Pasted image 20261012153106.png|421]]
3. **ALU operation:** perform the operation specified by the instruction, using `funct3` and `funct7` to determine the exact operation.
    ![[Pasted image 20261012153127.png]]
4. **Register write:** write the ALU result to `rd`, selected by bits `[11:7]`.
    ![[Pasted image 20261012153139.png]]

```
R[rd] = R[rs1] OP R[rs2]
```

The result is written only when register writing is enabled.

## 5. Arithmetic-Logical Instructions with an Immediate

Example syntax:

```
addi rd, rs1, imm
```

The instruction uses one register operand and a 12-bit immediate, which is sign-extended to 64 bits.

### Execution in Four Steps

1. Fetch the instruction and calculate `PC + 4`.
2. Read the source register `rs1`.
3. The ALU performs the operation using `R[rs1]` and the sign-extended immediate.
4. Write the result to `rd`.

![[Pasted image 20261012160205.png|561]]

For an addition immediate instruction:

```
R[rd] = R[rs1] + sign_extend(imm12, 64)
```

Unlike an R-type instruction, the second ALU operand comes from the immediate rather than `rs2`.

## 6. Load Instructions — I-Type

Example syntax:

```
ld rd, offset12(rs1)
```

A load reads a value from Data Memory and stores it in a register.

### Execution in Five Steps

1. Fetch the instruction and calculate `PC + 4`.
2. Read the base register `rs1`.
3. Sign-extend the 12-bit offset and calculate the effective address using the ALU.
4. Read the data from Data Memory at that address.
5. Write the memory data to destination register `rd`.

![[Pasted image 20261012160943.png]]

```
offset_ext = sign_extend(imm12, 64)
address    = R[rs1] + offset_ext
R[rd]      = Memory[address]
```

Required blocks:

- Register File to read the base register and write the result.
- Sign-extension unit to generate the 64-bit offset.
- ALU to calculate the effective address.
- Data Memory, controlled by `MemRead`.

The destination receives the value read from memory, **not the address calculated by the ALU**.

## 7. Store Instructions — S-Type

Example syntax:

```
sd rs2, offset12(rs1)
```

A store writes a register value into Data Memory.

### Execution in Four Steps

1. Fetch the instruction and calculate `PC + 4`.
2. Read the base register `rs1` and the data register `rs2`.
3. Sign-extend the offset and calculate the effective address using the ALU.
4. Write the contents of `rs2` to Data Memory at that address.

![[Pasted image 20261012161345.png]]

```
offset_ext = sign_extend(imm12, 64)
address    = R[rs1] + offset_ext
Memory[address] = R[rs2]
```

Required blocks:

- Register File to read `rs1` and `rs2`.
- Sign-extension unit.
- ALU to calculate the effective address.
- Data Memory, controlled by `MemWrite`.

**Key distinction:** A load transfers data from memory to a register; a store transfers data from a register to memory.

## 8. Building a Single-Cycle Processor

The functional blocks needed by the different instruction types are combined into one processor datapath.

In this implementation, **every instruction starts and finishes in one clock cycle**.

This imposes two important constraints:

1. A resource cannot be used more than once by the same instruction.
2. Any resource needed more than once must be duplicated, except the Register File, which has separate read and write ports.

Therefore, the implementation uses separate:

- Instruction Memory.
- Data Memory.

These memories are assumed to be implemented as caches to provide fast access.

### 8.1 Sharing Functional Units with Multiplexers

Functional units can be shared by different instruction types. When an input can receive data from alternative sources, a multiplexer selects the correct input according to the control signals.

Two important multiplexers are:

**MUX A — ALU second operand**

Selects between:

- The second register operand `R[rs2]`, for R-type instructions.
- The sign-extended immediate or offset, for immediate and load/store instructions.

Its control signal is `ALUSrc`.

**MUX C — Register write-back data**

Selects between:

- The ALU result, for arithmetic-logical instructions.
- The data read from Data Memory, for load instructions.

Its control signal is `MemtoReg`.

These multiplexers allow the same ALU and Register File to serve multiple instruction types.

## 9. Conditional Branch Instructions — B-Type

The reduced processor supports conditional branches:

```
beq rs1, rs2, offset
bne rs1, rs2, offset
```

A branch compares two register values and determines the next value of the PC.

### 9.1 Branch Execution in Four Steps

1. Fetch the instruction and calculate `PC + 4`.
2. Read source registers `rs1` and `rs2`.
3. The ALU compares the register values by subtraction, while an adder calculates the branch target address.
4. Select the next PC:
    - If the branch condition is true, use `PC + offset_ext`.
    - Otherwise, use `PC + 4`.

```
BTA = PC + (sign_extend(branch_imm, 64) << 1)
```

The branch offset is relative to the address of the branch instruction itself, not to `PC + 4`.

![[Pasted image 20261012163029.png|629]]

### 9.2 Required Functional Blocks

- Register File: reads both source operands.
- ALU: compares the operands and generates the `Zero` output.
- Branch adder: calculates the Branch Target Address.
- Sign-extension unit and left shifter: reconstruct the byte offset.
- Multiplexer B: selects the next PC.

### 9.3 The `Zero` Signal and `beq`

The ALU compares the operands by subtraction:

```
Zero = 1  -> operands are equal
Zero = 0  -> operands are different
```

For `beq`, the branch is taken when `Zero = 1`.

The control signal selecting the next PC is:

```
PCSrc = Zero AND Branch
```

- `PCSrc = 1`: select the branch target address.
- `PCSrc = 0`: select `PC + 4`.

### 9.4 Extending the Processor to Support `bne`

`beq` and `bne` share the same opcode:

```
opcode = 1100011
```

They differ in `funct3`:

```
beq: funct3 = 000
bne: funct3 = 001
```

They can therefore be distinguished using instruction bit `instr[12]`.

- `beq`: branch when `Zero = 1`.
- `bne`: branch when `Zero = 0`.

The control logic generating `PCSrc` must take into account `Branch`, `Zero`, and the instruction's branch type.

## 10. ALU Control Unit

The ALU controller generates the 4-bit `OperationALU` control signal based on:

- The 2-bit `ALUOp`, derived from the opcode/control unit.
- `funct3` and `funct7` fields for R-type instructions.

According to the lecture:

- `ALUOp = 00` or `01`: `funct3` and `funct7` are treated as don't-care fields.
- `ALUOp = 10` (R-type instructions): the controller uses bit 30 of `funct7` and bits `[14:12]` (`funct3`) to determine the ALU operation.

The ALU control unit therefore combines the broad instruction category with the instruction's function fields.

![[Pasted image 20261012163204.png]]

## 11. Execution Summary

### R-Type

```
Fetch -> Read rs1, rs2 -> ALU operation -> Write rd
```

### Load (`ld`)

```
Fetch -> Read rs1 -> Calculate address -> Read memory -> Write rd
```

### Store (`sd`)

```
Fetch -> Read rs1, rs2 -> Calculate address -> Write memory
```

### Conditional Branch (`beq` / `bne`)

```
Fetch -> Read rs1, rs2 -> Compare and calculate BTA -> Update PC
```

All instruction types share the initial fetch stage and register decoding, but their remaining operations differ.

## 12. Operative code and control signal

![[Pasted image 20261012163420.png]]

## 13. Single-Cycle Timing and Critical Path

A single-cycle processor starts and completes one instruction per clock cycle. The clock period must be long enough for the slowest instruction to complete.

The **critical path** is the longest sequence of functional units traversed by an instruction.

According to the lecture's example:

![[Pasted image 20261012163556.png]]

|Instruction type|Execution time|
|---|---|
|R-type|600 ps|
|Conditional branch|500 ps|
|Store|700 ps|
|Load|800 ps|

The load instruction is the critical path because it uses five functional units in series.

Therefore:

```
Clock period T = 800 ps
Clock frequency f = 1 / T = 1.25 GHz
```

The clock period must be the same for all instructions, even if some finish their work sooner.

**Limitation:** The single-cycle implementation is not very efficient because shorter instructions are forced to use the same 800 ps clock period as the longest instruction.

## Exam Essentials

- RISC-V is a **load/store architecture**: ALU operands come from registers; memory is accessed through load/store instructions.
    
- Instruction length in this implementation: **32 bits**; register width: **64 bits**.
    
- Register File: **32 × 64-bit registers**, two read ports, one write port.
    
- `PC + 4` is the address of the next sequential instruction.
    
- R-type instructions read two registers, use the ALU, and write `rd`.
    
- Immediate arithmetic uses a sign-extended immediate as the second ALU operand.
    
- `ld` calculates an address, reads Data Memory, and writes the loaded value to `rd`.
    
- `sd` calculates an address and writes `R[rs2]` to Data Memory.
    
- Load/store effective address: `R[rs1] + offset_ext`.
    
- Branch target: `PC + (sign-extended branch immediate << 1)`.
    
- `beq` branches when the ALU's `Zero = 1`; `bne` branches when `Zero = 0`.
    
- `PCSrc` controls the choice between `PC + 4` and the branch target.
    
- `ALUSrc` selects the ALU's second operand; `MemtoReg` selects the register write-back data.
    
- `RegWrite`, `MemRead`, and `MemWrite` control register writing and memory access.
    
- The ALU controller uses `ALUOp`, `funct3`, and, for R-type instructions, relevant `funct7` bits.
    
- In the lecture's single-cycle example, the **load instruction determines the 800 ps clock period**, giving a maximum frequency of **1.25 GHz**.