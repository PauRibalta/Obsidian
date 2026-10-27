## 1. Pipeline Hazards

A **hazard** occurs when an instruction cannot proceed normally in the pipeline.

Three types of hazards:

- **Structural hazards:** different instructions attempt to use the same hardware resource in incompatible ways during the same clock cycle. Example: a single memory shared by instructions and data.
- **Data hazards:** an instruction needs a result that is not yet available in the pipeline.
- **Control hazards:** the next instruction to fetch depends on a decision that has not yet been made. Example: conditional branches.

## 2. Control Hazards in Conditional Branches

Consider the RISC-V instruction:

```
beq rs1, rs2, offset
```

The branch is taken if `rs1 == rs2`.

In the standard five-stage pipeline:

|Stage|Branch operation|
|---|---|
|IF|Fetch instruction and increment PC|
|ID|Read source registers `rs1` and `rs2`|
|EX|Evaluate the condition and calculate the branch target address|
|MEM|Determine which PC to use|
|WB|Final pipeline stage|

In the pipeline described in the slides, the condition and branch target address are calculated in **EX**, but the branch control decision is completed in **MEM**.

The processor must fetch an instruction every cycle to keep the pipeline supplied, but it does not yet know whether to fetch the next sequential instruction (`PC + 4`) or the branch target address (BTA).

The delay of three clock cycles before the correct next instruction is determined is the **control hazard**, also called the _conditional branch hazard_.

Control hazards are statistically less frequent than data hazards, according to the slides.

## 3. Solution 1: Standard Pipeline with Stalls

The simplest solution is to prevent the PC and the IF/ID pipeline register from being updated for three cycles after a conditional branch.

This introduces **three stalls in IF**.

- The processor waits until the branch decision is known.
- The pipeline behaves correctly whether the branch is taken or not taken.
- Performance decreases because three cycles are lost for each conditional branch.

The key idea is to delay fetching new instructions until the correct PC is available.

## 4. Solution 2: Standard Pipeline with Branch Prediction

Instead of stalling for every branch, predict that the branch is **not taken** and continue fetching sequential instructions using `PC + 4`.

The prediction is checked at the end of MEM.

### Case A: Prediction Correct — Not Taken

If the branch is not taken:

- The sequential instructions fetched were correct.
- Execution continues without flushing those instructions.

### Case B: Prediction Incorrect — Taken

If the branch is taken:

- The processor should have fetched from the branch target.
- The three instructions already in the pipeline after the branch must be discarded.
- This operation is called **flushing**.
- The discarded instructions must not write results to memory or the register file.
- They are effectively converted into NOPs or equivalent stalls.

Branch prediction improves performance when the prediction is correct, but a misprediction still incurs a penalty.

## 5. Solution 3: Optimized Pipeline for Branches

The goal is to reduce the branch penalty from three instructions to **one instruction to discard or one stall**.

The key idea is to move branch processing earlier, from **MEM to ID**.

Two operations must be moved to ID:

1. Calculation of the branch target address (BTA).
2. Evaluation of the register comparison that determines whether the branch is taken.

### 5.1 Calculate the Branch Target Address Earlier

Once the branch instruction has been fetched, its PC and offset are available in the IF/ID pipeline register.

Therefore, move the branch-target adder from EX to ID.

### 5.2 Evaluate the Branch Condition Earlier

The source registers are read in ID, so the values can be compared in that stage.

For `beq`, the comparison can be implemented using:

1. A bitwise XOR of the two register values.
2. A NOR operation over the XOR outputs.

If every XOR output is zero, the values are equal and the NOR output is `1`. Therefore, the `beq` condition is satisfied.

### 5.3 Optimized Pipeline Behaviour

The ID stage now:

- Evaluates the branch condition.
- Calculates the branch target address.
- Determines which PC to use.

Even with this optimization, the slides require **one NOP or one IF stall for every conditional branch** to ensure correct operation for both branch outcomes.

## 6. Optimized Pipeline with Branch Prediction

The optimized pipeline can also predict that the branch is not taken and continue fetching sequential instructions.

The prediction is checked at the end of ID, rather than MEM.

### Case A: Prediction Correct — Not Taken

The sequential instructions are correct, so execution continues without flushing.

### Case B: Prediction Incorrect — Taken

Only the instruction immediately following the branch has already entered the pipeline and needs to be discarded.

The processor flushes this instruction and fetches from the branch target.

The `**IF.Flush**` **control signal** clears the part of the IF/ID pipeline register containing the fetched instruction. This turns the instruction into a NOP, which propagates through the pipeline.

### Comparison of Branch Solutions

|Approach|Decision stage|Behaviour|Branch penalty described in slides|
|---|---|---|---|
|Standard pipeline with stalls|MEM|Wait for the decision|3 stalls|
|Standard pipeline with prediction|MEM|Predict not taken; flush if wrong|3 instructions flushed on a taken branch|
|Optimized pipeline without prediction|ID|Resolve branch earlier|1 stall/NOP per branch|
|Optimized pipeline with prediction|ID|Predict not taken; flush if wrong|1 instruction flushed on a taken branch|

The penalty refers to the behaviour described for these particular pipeline designs in the slides.

## 7. Data Hazards in the Optimized Pipeline

Moving branch comparison to ID introduces a new data-hazard problem: a branch may need an operand that a preceding instruction has not yet written to the register file.

Because the comparison happens in ID, the processor may need a new forwarding path.

### 7.1 Previous Instruction Produces One Branch Operand

Example:

```
addi x1, x1, 4
add  x4, x2, x3
beq  x5, x1, L1
```

The branch needs the updated value of `x1`, produced by the preceding `addi`.

The solution is a new **EX/ID forwarding path** that forwards the value of `x1` from the EX/MEM pipeline register to the branch comparison in ID.

The slides note that this EX/ID forwarding path and its two multiplexers are not shown in the pipeline figures from the book and the course handout.

### 7.2 Immediately Preceding Instruction Produces a Branch Operand

Example:

```
addi x1, x1, 4
beq  x5, x1, L1
```

The branch needs `x1`, which is calculated in EX by the immediately preceding instruction.

The solution described in the slides is:

- Insert **one cycle of stall**.
- Forward `x1` from EX/MEM to ID through the EX/ID forwarding path.

The stall delays the branch comparison until the value can be forwarded.

### 7.3 Both Branch Operands Come from Immediately Preceding Instructions

Example:

```
addi x1, x1, 4
add  x5, x2, x3
beq  x5, x1, L1
```

The branch depends on both preceding instructions:

- `x1` is produced by `addi`.
- `x5` is produced by `add`.

The solution is **one cycle of stall plus EX/ID forwarding of** `**x5**` **from EX/MEM**.

The slides explain that `x1` can be written to and read from the register file in the same cycle, CK5, so it does not require the same forwarding treatment.

## 8. Load-Use Hazard in Branch Comparison

Consider a branch that immediately follows a load:

```
ld  x1, 20(x3)
beq x5, x1, L1
```

The load obtains `x1` from memory, so its value becomes available later than the result of an arithmetic instruction.

The optimized pipeline must insert **two cycles of stall** before the branch can compare the operand.

The slides explain that the value of `x1` can be written to and read from the register file in the same cycle, CK5.

### Data-Hazard Summary for Branches

|Producer of branch operand|Solution described in slides|
|---|---|
|Previous instruction, value available from EX/MEM|EX/ID forwarding|
|Immediately preceding arithmetic instruction|1 stall + EX/ID forwarding|
|Both operands from immediately preceding instructions|1 stall + EX/ID forwarding for `x5`; `x1` uses register-file write/read in CK5|
|Immediately preceding load instruction|2 stalls|

## Exam Essentials

- **Structural hazard:** competing instructions need the same hardware resource.
    
- **Data hazard:** an instruction needs a result that is not yet available.
    
- **Control hazard:** the next PC depends on an unresolved branch.
    
- In the standard pipeline, the branch condition and target are calculated in EX, but the decision is made in MEM.
    
- The standard pipeline can insert **3 stalls** or predict not taken and flush **3 instructions** if the prediction is wrong.
    
- The optimized pipeline moves branch comparison and target calculation to ID, reducing the penalty to **1 stall/NOP** or **1 flushed instruction** with prediction.
    
- For `beq`, equality can be detected by XOR followed by NOR.
    
- `IF.Flush` clears the fetched instruction in IF/ID, effectively converting it into a NOP.
    
- Moving branch comparison to ID creates additional data hazards and requires an **EX/ID forwarding path**.
    
- An immediately preceding arithmetic instruction requires **1 stall + forwarding** in the cases shown.
    
- An immediately preceding load requires **2 stalls**.
    
- Be able to explain the pipeline timing and justify the number of stalls for each dependency pattern.