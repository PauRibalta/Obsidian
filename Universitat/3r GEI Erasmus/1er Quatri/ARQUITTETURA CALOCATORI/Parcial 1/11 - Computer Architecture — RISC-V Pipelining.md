## 1. Pipelining: Basic Concepts

**Pipelining** improves processor performance by overlapping the execution of multiple instructions belonging to a sequential instruction stream.

- Instruction execution is divided into **pipeline stages**, each performing part of the work.
- Instructions enter the pipeline, move through successive stages, and exit at the other end.
- Pipeline execution is transparent to the programmer.

### Latency vs. Throughput

- **Latency:** Time required to complete a single instruction.
- **Throughput:** Number of instructions completed per unit of time.

Pipelining is analogous to an assembly line: it does not necessarily reduce the time required to complete one item, but it increases the number of items completed over time.

**Laundry example:**

- Four sequential stages, each taking 30 minutes.
- Sequential execution: one load completed every 2 hours.
- Pipelined execution: one load completed every 30 minutes once the pipeline is full.
- Throughput improves by a factor of 4, while the latency of each load remains 2 hours.

## 2. Pipeline Timing and Performance

Ideally, an instruction advances by one stage during each pipeline clock cycle.

Because the stages operate synchronously, the clock period is determined by the **slowest pipeline stage**.

- The design objective is to balance the duration of the stages.
- Perfectly balanced stages provide the maximum ideal speedup.
- With (n) balanced stages, ideal throughput speedup is (n).

### Five-stage RISC-V pipeline

The RISC-V instructions considered in the slides use up to five stages, because a load instruction requires all five.

- Pipeline stages: 5
- Clock period: 200 ps
- Instruction latency: (5 \times 200 = 1000\text{ ps} = 1\text{ ns})
- Clock frequency: (f = 1/(200\text{ ps}) = 5\text{ GHz})

The slides state a 1 GHz frequency alongside the 1000 ps instruction latency on page 8, but that corresponds to the single-cycle processor period. For the five-stage pipeline with a 200 ps clock period, the frequency is 5 GHz.

For balanced stages, the comparison is:

|Metric|Single-cycle processor|Five-stage pipeline|
|---|---|---|
|Clock period|1000 ps|200 ps|
|Instruction latency|1000 ps|1000 ps|
|Ideal throughput|1 instruction / 1000 ps|1 instruction / 200 ps|
|Throughput speedup|1×|5×|

The pipeline improves throughput, not the latency of an individual instruction in this balanced example.

## 3. RISC-V Instruction Characteristics

RISC-V instruction encoding simplifies instruction fetch and decoding:

- All instructions are **32 bits** long.
- There are six different instruction formats.
- The fields identifying `rs1`, `rs2`, and `rd` are always in the same positions.
- Memory operands occur only in load and store instructions.
- Memory accesses require operand alignment, allowing data transfer with a single memory access.

These characteristics simplify the IF and ID stages.

## 4. The Five Pipeline Stages

| Stage   | Name               | Main function                                | **What do we use** |
| ------- | ------------------ | -------------------------------------------- | ------------------ |
| **IF**  | Instruction Fetch  | Fetch instruction and increment PC           | Instruction caché  |
| **ID**  | Instruction Decode | Decode instruction and read source registers | Register File      |
| **EX**  | Execution          | Perform ALU operation or calculate address   | ALU                |
| **MEM** | Memory Access      | Read or write data memory                    | Data caché         |
| **WB**  | Write Back         | Write result into destination register       | Register File      |

### Stage usage by instruction type

**Arithmetic and logical instructions:** `op rd, rs1, rs2`

1. IF: Fetch instruction and increment PC.
2. ID: Read `rs1` and `rs2`.
3. EX: Execute `rs1 op rs2`.
4. MEM: No data-memory operation.
5. WB: Write result to `rd`.

**Load instructions:** `ld rd, offset12(rs1)`

1. IF: Fetch instruction and increment PC.
2. ID: Read base register `rs1`.
3. EX: Calculate effective address, `rs1 + offset_ext`.
4. MEM: Read `M[rs1 + offset_ext]`.
5. WB: Write loaded data to `rd`.

**Store instructions:** `sd rs2, offset12(rs1)`

1. IF: Fetch instruction and increment PC.
2. ID: Read base register `rs1` and data register `rs2`.
3. EX: Calculate effective address, `rs1 + offset_ext`.
4. MEM: Write `R[rs2]` to `M[rs1 + offset_ext]`.
5. WB: No register write-back.

**Conditional branch:** `beq rs1, rs2, offset12`

1. IF: Fetch instruction and increment PC.
2. ID: Read `rs1` and `rs2`.
3. EX: Compare registers using `rs1 - rs2` and calculate the branch target from `PC + offset_ext`.
4. MEM: No data-memory operation.
5. WB: No register write-back; the PC is updated according to the branch decision.

## 5. Pipeline Structure and Interstage Registers

The processor is divided into five stages, separated by **interstage pipeline registers**.

At each clock cycle:

- A new instruction enters IF.
- The previous instruction advances to ID.
- Other instructions advance to their next stages.
- Each instruction carries the information needed for its remaining execution stages.

Once the pipeline is full, up to **five different instructions** can be in execution during the same clock cycle.

The interstage registers contain information belonging to different instructions; their contents must remain associated with the correct instruction.

### Pipeline registers

- `IF/ID`: Separates IF and ID.
- `ID/EX`: Separates ID and EX.
- `EX/MEM`: Separates EX and MEM.
- `MEM/WB`: Separates MEM and WB.

The pipeline diagrams in the slides assume that instructions have no data dependencies and that there are no branch instructions when illustrating the ideal overlap.

## 6. Interstage Register Timing

Interstage registers are **edge-triggered**, operating on the rising edge of the clock.

During a clock cycle, a stage's combinational logic operates on data from its input register and must produce valid results before the next active clock edge.

The timing constraint includes:

- (t_prop): Propagation delay from the input register.
- (t_comb): Combinational logic delay within the stage.
- (t_set_up): Setup time required by the output register.

The clock period must be long enough for the data to propagate through the stage and satisfy the setup requirement.

## 7. Propagating the Destination Register Address

A key pipeline issue occurs with load instructions.

The destination register address `rd` is identified early, during instruction decoding, but the loaded data becomes available only later, at the end of MEM.

Therefore, the destination register address must be propagated through the pipeline alongside the corresponding instruction and data.

For a load:

- The destination register address is obtained during ID.
- It passes through `ID/EX` and `EX/MEM`.
- It reaches `MEM/WB` together with the data to be written.
- During WB, the data is written to the correct destination register.

Without this propagation, the data could be written to the wrong register because the instruction in `IF/ID` may already be different from the instruction in `MEM/WB`.

## 8. Register File Timing and Dependencies

The Register File (RF) must support reading source operands and writing back results while several instructions overlap.

The slides use different clock edges for RF writes and reads:

- **Falling edge:** Complete register write-back.
- **Rising edge:** Read source registers for the next instruction.

This timing allows a value written during a clock cycle to be read later in that same cycle.

### Example: Independent loads

Consider:

```
ld  x10, 40(x1)
...
ld  x13, 48(x1)
```

During cycle C5:

- The first load is in WB and writes its result into `x10`.
- The second load is in ID and reads its base register `x1`.

The write occurs on the falling edge and the read on the rising edge.

### Example: Read-after-write dependency

Consider:

```
ld  x10, 40(x1)
...
ld  x13, 48(x10)
```

The second load needs `x10` as its base register. In the example shown in the slides, both operations occur during cycle C5:

1. The first load writes the new value into `x10` on the falling edge.
2. The second load reads `x10` on the rising edge.
3. The second load therefore reads the value just written.

**Key principle:** A register can be written on the falling edge and the newly written value read on the rising edge of the same clock cycle.

## 9. Pipeline Control Signals

Control signals must travel through the pipeline with the instruction they control.

The instruction fetched in IF is stored in `IF/ID`, together with its 64-bit PC. During ID, the instruction is decoded and the required control signals are generated.

These signals must be propagated through the appropriate interstage registers so that they reach the correct stage at the correct time.

The useful function-field bits must also be propagated from ID/EX to EX for ALU control:

- `instr[30]`
- `instr[14:12]`

Together, these fields provide the bits needed to generate the ALU control signals.

### Control signal groups

The final slide identifies three groups of control signals:

- **WB (2 bits):** `RegWrite`, `MemtoReg`
- **M (3 bits):** `Branch`, `MemWrite`, `MemRead`
- **EX (3 bits):** `ALUOp` (2 bits), `ALUSrc`

These signals are carried forward through the pipeline and used in the appropriate execution stages.

## 10. Interstage Register Contents

The final slide specifies the following fields:

|Pipeline register|Contents|
|---|---|
|`IF/ID`|`PC` (64 bits), instruction (32 bits)|
|`ID/EX`|WB controls (2 bits), M controls (3 bits), EX controls (3 bits), PC (64 bits), `Rs1` value (64 bits), `Rs2` value (64 bits), extended offset (64 bits), `funct7_3` (4 bits), `rd` (5 bits)|
|`EX/MEM`|WB controls (2 bits), M controls (3 bits), PC (64 bits), `ALU_out` (64 bits), `Zero` bit (1 bit), `Rs2` value (64 bits), `rd` (5 bits)|
|`MEM/WB`|WB controls (2 bits), data read from memory (64 bits), `ALU_out` (64 bits), `rd` (5 bits)|

Notes:

- `Rs1` and `Rs2` refer to the 64-bit contents of the source registers.
- `rd` is the 5-bit destination register address.
- `funct7_3` contains the required function bits: `instr[30]` and `instr[14:12]`.
- `ALUOp` occupies two bits; `ALUSrc` occupies one bit.

## Exam Essentials

- Pipelining overlaps instruction execution to improve **throughput**, not necessarily individual instruction latency.
    
- A five-stage pipeline has stages **IF, ID, EX, MEM, WB**.
    
- The clock period is determined by the slowest stage.
    
- With five perfectly balanced stages, ideal throughput speedup is **5×**.
    
- Pipeline registers separate stages and carry instruction-specific information forward.
    
- The destination register address `rd` must travel alongside the corresponding instruction and result.
    
- In the presented RF timing scheme, writes occur on the falling edge and reads on the rising edge, allowing a same-cycle write followed by a read.
    
- Control signals must be propagated through the pipeline to reach the stage where they are used.
    
- Know the fields stored in `IF/ID`, `ID/EX`, `EX/MEM`, and `MEM/WB`.