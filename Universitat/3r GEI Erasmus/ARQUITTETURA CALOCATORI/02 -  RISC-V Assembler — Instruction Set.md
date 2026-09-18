## 1. Instruction Categories

Assembler instructions can be classified into:

1. **Arithmetic / logic**
    
2. **Memory transfer**
	1. From memory to registers→ load
	2. From registers to memory→ store
	3. Registers transfer→ move
    
3. **Control flow** → branch / jump / jump and link / return
    
4. Data transfer in entrance and exit **In/Out**
    

RV64i instructions have a fixed size:

32 bits=4 bytes32 bits=4 bytes
![[Pasted image 20260918111124.png]]

An instruction can reference up to:

- `rs1` → source register 1
    
- `rs2` → source register 2
    
- `rd` → destination register
    

---

## 2. Arithmetic Instructions

### Addition and Subtraction

```asm
add rd, rs1, rs2
sub rd, rs1, rs2
```

Example:

```asm
add s0, s1, s2 → a = b + c
```

when `a`, `b`, `c` are allocated to `s0`, `s1`, `s2`.

RISC-V arithmetic instructions normally have **three operands**.

Expressions with more operands must therefore be decomposed:

```asm
add t0, s1, s2
add t0, t0, s3
add s0, t0, s4
```

---

## 3. Logical Instructions

Operations are performed **bit by bit**:

```asm
and rd, rs1, rs2
or  rd, rs1, rs2
xor rd, rs1, rs2
```

### Logical NOT

Pseudo-instruction:

```asm
not rd, rs
```

Equivalent to:

```asm
xori rd, rs, -1
```

because `-1` in two's complement is all ones.

---

## 4. Immediate Instructions

### `addi`

```asm
addi rd, rs1, imm → rd= rs1+imm
```

The immediate:

- Is **12-bit signed**.
    
- Is represented in two's complement.
    
- Is sign-extended to 64 bits.

There is no `subi`.

To subtract a constant:

```asm
addi rd, rs1, -constant
```

The same sign extension applies to:

```asm
andi
ori
xori
```

How to extend 12 bits constants:

```
12 bits:
5_10: 0000 0000 0101
64 bits:
5_10: 0000 ...... 0000 0000 0101

12 bits:
-5_10: 1111 1111 1011 (Flip the numbers and add 1)
64 bits:
-5_10: 1111 ..... 1111 1111 1011

```

---

## 5. Multiplication, Division and Remainder

These belong to the extended **RV64im** instruction set discussed in the course.

### Multiplication

```asm
mul rd, rs1, rs2
mulu rd, rs1, rs2

rs1, rs2 → 32 bits
rd → 64 bits
```

- `mul` → signed.
    
- `mulu` → unsigned.
    

### Division

```asm
div rd, rs1, rs2
divu rd, rs1, rs2
```

- `div` → signed.
    
- `divu` → unsigned.
    

### Remainder

```asm
rem rd, rs1, rs2
remu rd, rs1, rs2
```

- `rem` → signed.
    
- `remu` → unsigned.
    

Important:

- `add` / `addi` do not need signed/unsigned versions.
    
- `mul`, `div`, `rem` have signed and unsigned versions.
    

---

## 6. Shift Instructions

### Shift Left

```asm
slli rd, rs1, k    # rd <- rs1 << k: shift left k_10 bit
```
### Shift Right

```asm
srli rd, rs1, k    # rd <- rs1 >> k: shift right k_10 bit
```

Example:

```asm
slli s0, s0, 2
```

is equivalent to multiplying by 4:

s0 iniziale: 0000 . . . 0000 0000 0000 0000 0000 1010 
s0 finale: 0000 . . . 0000 0000 0000 0000 0010 1000

and:

```asm
srli s1, s1, 2
```

corresponds to division by 4:

s1 iniziale: 0000 . . . 0000 0000 0000 0000 0010 1000 
s1 finale: 0000 . . . 0000 0000 0000 0000 0000 1010

---

## 7. Comparison Instructions

### Register vs Register

```asm
slt rd, rs1, rs2  -> rd= 1 if rs1<rs2 0 otherwise 
slt rd, rs1, 8  -> rd= 1 if rs1<8 0 otherwise

```

### Register vs Immediate

```asm
slti rd, rs1, imm
```

The immediate is sign-extended before comparison.

`s l t` / `slti` perform **signed comparisons**.

For unsigned values such as addresses, use:

```asm
sltu
sltui
```

---

## 8. Pseudo-Instructions

Pseudo-instructions are convenient symbolic instructions automatically expanded by the assembler.

|Pseudo-instruction|Native instruction|
|---|---|
|`mv rd, rs`|`addi rd, rs, 0`|
|`nop`|`addi zero, zero, 0`|
|`not rd, rs`|`xori rd, rs, -1`|
|`neg rd, rs`|`sub rd, zero, rs`|
|`j offset`|`jal zero, offset`|
|`jr rs1`|`jalr zero, 0(rs1)`|
|`jal offset`|`jal ra, offset`|
|`jalr rs1`|`jalr ra, 0(rs1)`|
|`ret`|`jalr zero, 0(ra)`|

---

## 9. Load and Store

### Load

Transfers data:

```text
Memory → Register
```

```asm
ld rd, offset(rs1)
```

### Store

Transfers data:

```text
Register → Memory
```

```asm
sd rs2, offset(rs1)
```

### Data Sizes

|Load / Store|Size|
|---|--:|
|`ld` / `sd`|64 bits|
|`lw` / `sw`|32 bits|
|`lh` / `sh`|16 bits|
|`lb` / `sb`|8 bits|

Unsigned loads:

```asm
lwu
lhu
lbu
```

use **zero extension**.

Signed loads:

```asm
lw
lh
lb
```

use **sign extension**.

---

## 10. Base + Offset Addressing

For:

```asm
ld rd, offset(rs1)
```

the effective address is:

Address=rs1+sign-extended(offset)Address=rs1+\text{sign-extended}(offset)

The offset is a **12-bit signed value** expressed in bytes.

Example:

```asm
ld t0, 100(s1)
```

means:

t0=Memory[s1+100]t0=Memory[s1+100]

Similarly:

```asm
sd s2, 100(s1)
```

means:

Memory[s1+100]=s2Memory[s1+100]=s2

---

## 11. `.eqv`

`.eqv` defines a symbolic constant.

```asm
.eqv NUM1, 100
.eqv NUM2, 200
```

It behaves similarly to:

```c
#define NUM1 100
```

Important:

- `.eqv` is **not an instruction**.
    
- It does **not allocate memory**.
    
- It defines a symbolic value.
    

---

## 12. `li` — Load Immediate

```asm
li rd, constant
```

Loads a constant into a register.

Example:

```asm
li t0, 4
```

The course treats `li` as a pseudo-instruction.

For a 32-bit constant it can be expanded using:

```asm
lui
addi
```

---

## 13. `la` — Load Address

```asm
la rd, LABEL
```

Loads the **address** represented by `LABEL`.

Important distinction:

```asm
la t0, A     # address of A
ld t1, 0(t0) # value stored at A
```

Conceptually, `la` uses:

```asm
auipc
addi
```

for PC-relative address construction.

---

## 14. Control Flow

Normally execution is sequential:

PC←PC+4

Branch and jump instructions modify this flow.

Two main types:

- **Conditional branch**
    
- **Unconditional jump**
    

---

## 15. Conditional Branches

Alter the order of the execution of the instructions:
The next instruction is not necessarly the next one, but the one in the destination of the jump.

```asm
beq rs1, rs2, ind_salto
bne rs1, rs2, ind_salto
blt rs1, rs2, ind_salto
bge rs1, rs2, ind_salto
```

PC <= PC + ind_salto

|Instruction|Condition|
|---|---|
|`beq`|`rs1 == rs2`|
|`bne`|`rs1 != rs2`|
|`blt`|`rs1 < rs2`|
|`bge`|`rs1 >= rs2`|

Branches use PC-relative addressing.

---

## 16. Unconditional Jumps

### j

```asm
j L1 # go to instruction L1
```

Jump without saving a return address.

Equivalent:

```asm
jal zero, LABEL
```

### jal

```asm
jal L1 # go to L1 and save return address 
	   # in register ra
```

For a function call:

```asm
jal ra, FUNCTION
```

The return address is stored in `ra`.

### jr

```asm
jr ra # go to address contained in ra
```

### Return

```asm
ret
```

Equivalent to:

```asm
jalr zero, 0(ra)
```

![[Pasted image 20260918113708.png]]
![[Pasted image 20260918113728.png]]

---

## 17. Labels

Labels provide symbolic names for instruction/data addresses.

Example:

```asm
LOOP:
    ...
    j LOOP
```

The assembler resolves the label to the corresponding address/offset.

--- 
## Summary

![[Pasted image 20260918113913.png]]
![[Pasted image 20260918113945.png]]
![[Pasted image 20260918114044.png]]


---

## Exam Essentials

- RV64i instructions = **32 bits**.
    
- Arithmetic instructions normally use **3 registers**.
    
- `add`, `sub`, `and`, `or`, `xor` → register-register.
    
- `addi`, `andi`, `ori`, `xori` → immediate.
    
- Immediate = **12-bit signed**, sign-extended.
    
- `slli` → shift left; `srli` → shift right.
    
- `slt` → signed comparison.
    
- `sltu` → unsigned comparison.
    
- `ld/sd` → 64-bit.
    
- `lw/sw` → 32-bit.
    
- `lh/sh` → 16-bit.
    
- `lb/sb` → 8-bit.
    
- `la` = **address**.
    
- `ld` = **content/value**.
    
- `li` = **constant**.
    
- `beq/bne/blt/bge` = conditional branches.
    
- `jal` = jump + save `PC+4`.
    
- `jalr` = register-based jump + save `PC+4`.
    
- `ret` = `jalr zero, 0(ra)`.
    
- `.eqv` defines a constant and allocates no memory.