## 1. General Instruction Format

All RV64i instructions have:

32 bits

The instruction type is identified mainly through the **7-bit opcode**.
![[Pasted image 20260918115151.png]]

Instructions can reference:

- `rs1` → source register
    
- `rs2` → source register
    
- `rd` → destination register
    
- `imm` → immediate value
    

Register fields always occupy fixed positions across formats where they are present.

![[Pasted image 20260918115136.png]]

---

## 2. Six Instruction Formats

RISC-V uses six main formats:

| Format | Main use                           |              Immediate | Immediate extension (64 bits) |
| ------ | ---------------------------------- | ---------------------: | ----------------------------- |
| **R**  | Register-register arithmetic/logic |                      — | —                             |
| **I**  | Immediate, loads, jalr             |                12 bits | Sign extension                |
| **S**  | Stores                             |                12 bits | Sign extension                |
| **B**  | Conditional branches               | 12 bits, multiple of 2 | Sign extension                |
| **U**  | Load upper integer                 |                20 bits | Sign extension                |
| **J**  | Jump and link                      | 20 bits, multiple of 2 | Sign extension                |

![[Pasted image 20260918115420.png|411]]

---

## 3. R-Type

Used for register-register arithmetic/logic instructions.

![[Pasted image 20260918115506.png]]

Fields:

- `rs1` → first source operand.
    
- `rs2` → second source operand.
    
- `rd` → result.
    
- `funct3` / `funct7` → identify the exact operation.
    
- `opcode` → identifies the instruction class.
    

Example:

![[Pasted image 20260918115553.png|552]]

---

## 4. I-Type

Used for:

- Immediate arithmetic/logic.
    
- Loads.
    
- `jalr`.
    

![[Pasted image 20260918115636.png]]

Examples:

![[Pasted image 20260918115714.png]]
![[Pasted image 20260918115831.png]]

---

## 5. S-Type

Used for store instructions.
![[Pasted image 20260918115905.png]]

Example:

![[Pasted image 20260918115923.png]]

![[Pasted image 20260918115952.png]]

---

## 6. B-Type

Used for conditional branches:

![[Pasted image 20260918120106.png|610]]

Example:

![[Pasted image 20260918120154.png|565]]

---

## 7. J-Type

Used by `jal`.

![[Pasted image 20260918120305.png]]

---

## 8. jalr and I-Type

jalr uses the **I-type format**:

![[Pasted image 20260918120331.png]]


---
## 9. Immediate Reconstruction

The immediate is not always stored as one contiguous field.

|Format|Immediate fields|
|---|---|
|R|None|
|I|`imm[11:0]`|
|S|`imm[11:5]` + `imm[4:0]`|
|B|`imm[12]`, `imm[10:5]`, `imm[4:1]`, `imm[11]`|
|U|`imm[31:12]`|
|J|`imm[20]`, `imm[10:1]`, `imm[11]`, `imm[19:12]`|

The processor reconstructs the immediate during instruction decoding.

For I/S/B formats, the immediate is sign-extended.

For B/J, the reconstructed offset is converted from half-word units to byte units before being added to the PC.

---
## 10. 32-bit Constants

### Problem

I-type instructions can represent immediates of only **12 bits**.

Therefore, to load a **32-bit constant or address**, it must be split into:

- **HIGH** → 20 most significant bits
- **LOW** → 12 least significant bits

The value is reconstructed using two native instructions:

```
li rd, cost32
```

expands conceptually to:

```
lui  rd, %hi(cost32)
addi rd, rd, %lo(cost32)
```

  Main effects:

1. Loads the **20-bit HIGH** value into bits `[31:12]` of the lower 32 bits of `rd`.
2. Sets bits `[11:0]` to `0`.
3. Sign-extends the resulting 32-bit value to 64 bits.

Conceptually:

```
63              32 31                  12 11          0
+----------------+----------------------+--------------+
| sign extension |      HIGH (20)       | 0000...0000  |
+----------------+----------------------+--------------+
```

After `lui`, `addi` adds the **LOW 12 bits**:

```
lui  rd, HIGH
addi rd, rd, LOW
```

The important issue is that the 12-bit immediate of `addi` is **sign-extended**.

Therefore, the LOW part can be:

- **positive**
- **negative**

and this affects how HIGH must be constructed.

### Case 1 — LOW is positive

If bit `[11]` of LOW is `0`, the sign extension does not introduce unwanted `1`s.

The normal HIGH + LOW reconstruction gives the original constant.

### Case 2 — LOW is negative

If bit `[11]` of LOW is `1`, `addi` sign-extends LOW with `1`s.

Simply using the original HIGH would therefore produce an incorrect result.

To compensate, HIGH must be increased by `1`.

```
HIGH_corrected = HIGH + LOW[11]
```

More precisely:

```
imm_20bit = cost[31:12] + cost[11]
```

  ![[Pasted image 20260918120912.png]]

![[Pasted image 20260918120928.png]]


---

## 11. J-Type

![[Pasted image 20260918121248.png]]

---

## 12. Summary of types

![[Pasted image 20260918121325.png|656]]

---
## 13. Addressing Modes

An **addressing mode** specifies how an instruction refers to its operands.

RISC-V has **four addressing modes**:

1. **Immediate**
2. **Register**
3. **Base + offset**
4. **PC-relative**

A single instruction can use more than one addressing mode.

Example:

```
addi s0, s1, 4
```

uses:

- register addressing → `s1`
- immediate addressing → `4`

---

## 10. Register Addressing

The operand is contained in a processor register.

The instruction specifies the register number.

Example:

```
add s0, s1, s2
```

Here the operands are obtained directly from registers.

---

## 11. Immediate Addressing

The operand is a **constant contained directly in the instruction**.

Example:

```
addi s0, s1, 4
```

The value `4` is the immediate operand.

Immediate addressing is used to specify a constant value for a source operand.

---

## 12. Base + Offset Addressing

The operand is located in memory.

Its address is calculated as:
- Address = BaseRegister + Offset 


The instruction contains:

- a base register
- a constant offset

Used by:

- **I-type:** `ld`, `jalr`
- **S-type:** `sd`

The offset is a **12-bit two's-complement value**.

Example:

```
ld t0, 32(s3)
```

means:

[  
Address = s3 + 32  
]

---

## 13. PC-Relative Addressing

The operand/address is calculated relative to the **Program Counter**:

[  
\boxed{Address = PC + Offset}  
]

Used by:

- **B-type** → conditional branches
- **J-type** → unconditional jump with link

The offset is expressed in **half-words**:

- B → 12-bit offset
- J → 20-bit offset

Because the offset is expressed in half-words, the processor converts it to bytes by adding a `0` as the least significant bit.

---

## 14. Addressing Modes — Quick Summary

| Addressing mode | Operand/address calculation    |
| --------------- | ------------------------------ |
| Immediate       | Constant is inside instruction |
| Register        | Operand is in a register       |
| Base + offset   | Base register + offset         |
| PC-relative     | PC + offset                    |

### Examples

```
add s0, s1, s2
```

→ Register

```
addi s0, s1, 4
```

→ Register + Immediate

```
ld t0, 32(s3)
```

→ Base + Offset

```
beq s1, s2, L1
```

→ PC-relative

---

## 15. Machine-Level Instruction Format

RISC-V keeps important fields in fixed positions:

- `rs1`, `rs2`, `rd` → fixed positions
- `funct3` → fixed position
- `funct3` / `funct7` → extend the opcode
- immediate fields → position depends on instruction format

This simplifies instruction decoding.

---

# Exam Essentials

## 32-bit Constants

A 32-bit constant cannot fit in a 12-bit I-type immediate.

```
li rd, cost32
```

expands to:

```
lui  rd, %hi(cost32)
addi rd, rd, %lo(cost32)
```

Remember:

[  
%hi(cost32)=cost[31:12]+cost[11]  
]

[  
%lo(cost32)=cost[11:0]  
]

The `+ cost[11]` correction is essential because `addi` sign-extends its 12-bit immediate.

---

## Addresses

```
la rd, offset32
```

expands to:

```
auipc rd, %pcrel_hi(offset32)
addi  rd, rd, %pcrel_lo(offset32)
```

with:

[  
\Delta=ind-PC  
]

[  
%pcrel_hi=\Delta[31:12]+\Delta[11]  
]

[  
%pcrel_lo=\Delta[11:0]  
]

---

## U Format

```
imm[31:12] | rd | opcode
     20       5      7
```

Instructions:

```
lui
auipc
```

---

## Four Addressing Modes

1. **Immediate** → constant in instruction
2. **Register** → operand in register
3. **Base + offset** → `base + offset`
4. **PC-relative** → `PC + offset`

---

## Base + Offset

Used by:

```
ld
sd
jalr
```

[  
Address=BaseRegister+Offset  
]

Offset = **12-bit signed value**.

---

## PC-relative

Used by:

```
beq, bne, bge, blt
jal
```

[  
Address=PC+Offset  
]

For B/J, the encoded offset refers to **half-words**, so:

[  
Offset_{byte}=Offset_{encoded}\times2  
]

---

## Most Important Things to Remember

- All RISC-V instructions are **32 bits**.
- Register numbers use **5 bits** because there are 32 registers.
- `opcode` identifies the general instruction type.
- `funct3`/`funct7` further identify the exact operation.
- `lui` loads the upper 20 bits.
- `auipc` creates a PC-relative upper value.
- `li` is a pseudo-instruction for loading constants.
- `la` is a pseudo-instruction for loading addresses.
- `lui` + `addi` reconstruct a 32-bit constant.
- `auipc` + `addi` reconstruct a PC-relative address.
- B/J offsets are encoded in **half-words**.
- `jalr` uses a **byte offset**, so no extra `0` is added.
- Immediate construction and sign extension are handled in hardware during decoding.
---

# Exam Essentials

- RV64i instruction → **32 bits**
    
- Register identifier → **5 bits**
    
- `opcode` → **7 bits**
    
- **R-type** → register + register
    
- **I-type** → immediate, load, `jalr`
    
- **S-type** → store
    
- **B-type** → conditional branch
    
- **U-type** → `lui`, `auipc`
    
- **J-type** → `jal`
    

### Immediates

- I/S/B → **12 bits**
    
- U/J → **20 bits**
    
- I-type immediate → **sign-extended**
    
- B/J offsets → **PC-relative**
    
- B/J offsets → measured in **half-words**
    
- B/J byte offset → last bit is always **0**
    
- `jalr` offset → **12-bit byte offset** (no ×2)
    

### Memory Addressing

- **Base + offset:**
    
    Address=Base+sign-extended(offset)Address = Base + sign\text{-}extended(offset)
- Used by:
    
    - `ld`
        
    - `sd`
        
    - `jalr`
        
- **PC-relative:**
    
    Target=PC+offsetTarget = PC + offset
- Used by:
    
    - branches (`beq`, `bne`, ...)
        
    - `jal`
        
    - `auipc`
        

### Jumps

- `jal` → **J-type**
    
- `jalr` → **I-type**
    
- Both save:
    
    rd=PC+4rd = PC + 4
- `j` → `jal zero, offset`
    
- `jr` → `jalr zero, 0(rs1)`
    
- `ret` → `jalr zero, 0(ra)`
    

### Constants & Addresses

- `li` → load a constant
- `li` uses `lui + addi`
- `la` → load an address
- `la` uses `auipc + addi`
- 32-bit constant → **20-bit HIGH + 12-bit LOW**

- Important correction:
    
    `%hi(cost32) = cost[31:12] + cost[11]`
    
    `%lo(cost32) = cost[11:0]`
    
- PC-relative address:
    
    `Δ = Address - PC`
    
    `%pcrel_hi = Δ[31:12] + Δ[11]`
    
    `%pcrel_lo = Δ[11:0]`
### Important Formats

**R-type**

```text
funct7 | rs2 | rs1 | funct3 | rd | opcode
```

**I-type**

```text
imm[11:0] | rs1 | funct3 | rd | opcode
```

**S-type**

```text
imm[11:5] | rs2 | rs1 | funct3 | imm[4:0] | opcode
```

**B-type**

```text
imm[12] | imm[10:5] | rs2 | rs1 | funct3 | imm[4:1] | imm[11] | opcode
```

**U-type**

```text
imm[31:12] | rd | opcode
```

**J-type**

```text
imm[20] | imm[10:1] | imm[11] | imm[19:12] | rd | opcode
```

### Remember

- `lui` → **upper 20 bits**
    
- `auipc` → **PC + upper 20 bits**
    
- `li` → **constant**
    
- `la` → **address**
    
- `jal` → **PC-relative jump**
    
- `jalr` → **register + offset jump**
    
- `ld` → memory → register
    
- `sd` → register → memory
    
- `beq/bne/...` → conditional PC-relative branch
    
- `jal/jalr` → save `PC+4` unless `rd = zero`