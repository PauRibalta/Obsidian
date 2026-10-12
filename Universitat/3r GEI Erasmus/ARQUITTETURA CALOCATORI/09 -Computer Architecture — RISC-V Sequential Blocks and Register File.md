## 1. Sequential Block Library

Main sequential blocks:

- **Parallel register**
- **Shift register**
- **Register bank / Register File (RF)**
- **Memory**

A bus represents a group of signals carrying a multi-bit word. Its value changes over time; for example, a 4-bit bus may carry `0100` at time T1 and `1101` at time T2.

## 2. Parallel Register

A parallel register with (n \geq 1) bits consists of:

- (n) D master-slave flip-flops.
- (n) data inputs: `D0, ..., Dn-1`.
- (n) outputs: `Q0, ..., Qn-1`.
- A clock input `CK`.

**Operation:** At each active clock edge, the register captures the input word and stores it. The captured word appears at the outputs during the following cycle.

For a 4-bit register:

- Inputs: `D3, D2, D1, D0`
- Outputs: `Q3, Q2, Q1, Q0`

The register can be implemented using four D master-slave flip-flops synchronized to the **falling edge** of the clock.

**Important:** Using transparent D bistables instead would make the register transparent while the clock is high, so it would not behave as a proper edge-triggered register.

### 2.1 Parallel Register with Load

A parallel register can include a control input `L` (_Load_):

- `L = 1`: the input word is loaded into the register and appears at the output in the next cycle.
- `L = 0`: the register maintains its current stored value.

**Functional implementation:**

- One D master-slave flip-flop per bit.
- One 2-input multiplexer per bit.
- `L` controls the multiplexers to select between loading new input data and retaining the current register value.

Conceptually, for each bit:

```
L = 1  -> select new input data D
L = 0  -> select current output Q (feedback)
```

This feedback mechanism allows the register to preserve its state when loading is disabled.

## 3. Shift Register

A shift register is implemented using (n \geq 1) cascaded D flip-flops. It has:

- A serial input `S`.
- (n) parallel outputs `Q0, ..., Qn-1`.
- A clock input `CK`.

### 3.1 Right-Shift Register

At each active clock edge, the stored word shifts **one bit to the right**:

- The least significant bit, at the right, is discarded.
- The other bits move one position to the right.
- The serial input bit is inserted at the left.

For a 4-bit register, the bit movement is:

```
S -> Q3 -> Q2 -> Q1 -> Q0 -> discarded
```

The register can be implemented using four cascaded D master-slave flip-flops synchronized to the falling edge of the clock.

### 3.2 Other Shift-Register Variants

- **Left-shift register:** shifts bits to the left.
- **Universal shift register:** can shift either right or left, controlled by a direction-selection signal.
- **Shift register with parallel load:** supports shifting and loading a complete word in parallel
- **Universal parallel/shift register:** combines parallel-register and universal-shift-register functionality.

Input/output configurations include:

- Serial input / serial output.
- Parallel input / serial output.
- Parallel and/or serial input / parallel and/or serial output.

## 4. Register File (RF)

A **Register File** is a collection of parallel registers with the same size and functionality. It is commonly used in the CPU datapath to store data used by operations such as the ALU.

### 4.1 RISC-V Register File Characteristics

The example used in the lecture is a RISC-V Register File containing:

- **32 registers**
- **64 bits per register**
- **5-bit register addresses**
- **2 independent read ports**
- **1 write port**

The address width is 5 bits because:

```
2^5 = 32 registers
```

The Register File supports two operations.

**Read**

- Input: register address.
- Output: the 64-bit value stored in that register.
- Reading does not modify the internal state.
- No explicit read command is required.

**Write**

- Inputs:
    
    - 5-bit address of the destination register.
    - 64-bit data to be stored.
    - Explicit `Write` control signal.
- The data is stored in the addressed register on the active clock edge.

The two read ports allow two source registers to be accessed independently, while the write port updates one destination register. This matches the common instruction structure with two source registers and one destination register.

### 4.2 Write Port: Decoder

The write port uses a **5-to-32 decoder**.

- The 5-bit address is supplied to the decoder.
- The decoder activates the line corresponding to the selected register.
- The selected register line and the `Write` signal enable one register for writing.
- On the active clock edge, the selected register stores the 64-bit input data.

The decoder ensures that the write operation targets the addressed register.

![[Pasted image 20261012150003.png|402]]

### 4.3 Read Ports: Multiplexers

Each read port uses a **32-to-1 multiplexer** for each bit position, selecting the contents of one of the 32 registers.

For each read port:

- Input: a 5-bit register address.
- Selection: the address controls the 32-to-1 multiplexer.
- Output: the selected register's 64-bit value.

Since each register contains 64 bits, the selected output is 64 bits wide.

The two read ports operate independently:

- Read port 1 has its own address and multiplexer.
- Read port 2 has its own address and multiplexer.

Therefore, two potentially different registers can be read in parallel without changing the stored data.

![[Pasted image 20261012150259.png|393]]

## 5. Reading and Writing Across Clock Cycles

The lecture describes the following timing arrangement:

- Writing in cycle (i) occurs on the **falling edge** of the clock.
- Reading in cycle (i+1) occurs on the **rising edge** of the clock and returns the value written during cycle (i).

This timing allows the newly written value to be available for reading in the following cycle.

![[Pasted image 20261012150800.png|477]]

## Exam Essentials

- A parallel register stores an entire (n)-bit word at an active clock edge.
- A register with `Load` loads new data when `L = 1` and retains its current value when `L = 0`.
- A right-shift register discards the least significant bit and inserts the serial input at the most significant end.
- A Register File is a collection of same-sized registers used for fast storage in the CPU datapath.
	- The lecture's RISC-V RF has **32 × 64-bit registers**, addressed using **5 bits**.
- It has **2 independent read ports and 1 write port**.
- A **5-to-32 decoder** selects the register to write.
- Each read port uses a **32-to-1 multiplexer** controlled by its 5-bit address.
- Reading does not change the RF state; writing requires the explicit `Write` control signal and an active clock edge.
- Know the difference between **parallel loading**, **serial shifting**, **reading**, and **writing**.