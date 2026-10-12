## 1. Sequential Circuits

A digital circuit is **sequential** if its outputs depend not only on the current input values, but also on some **past values**.

Therefore, a sequential circuit has:

- **Present state** → information currently stored in the circuit.
    
- **Inputs** → current external values.
    
- **Next state** → state determined by present state + inputs.
    
- **Outputs** → generated from the circuit state/inputs.
    

The state represents a form of **memory**.

### Structure

A sequential circuit consists of:

- **Bistables** → store information bits.
    
- **Combinational logic** → processes information.
    
- **Feedback** → connects the current state to the logic determining the next state.
    

```text
Inputs
   ↓
Combinational Logic → Outputs
   ↑          ↓
   └── State ← Memory
```

The fundamental memory element is the **bistable**, which stores one bit.

---

# 2. Bistables

A bistable is a memory element characterized by two stable states:

```text
0
1
```

It maintains its stored state until one or more inputs force a state change.

### Two families

**Asynchronous bistables**

- No synchronization signal.
    
- State changes directly in response to input events.
    

**Synchronous bistables**

- Controlled by a synchronization signal called **clock**.
    
- State transitions can occur only according to clock events.
    

---

# 3. Asynchronous SR Bistable

The SR bistable has:

- `S` → Set
    
- `R` → Reset
    
- `Q` → stored state
    
- `!Q` → complement of the stored state
    

`Q` represents the stored bit.

It is implemented using cross-coupled NOR gates.

---

## 4. SR Bistable Operation

### Hold / Stable state

```text
S = 0
R = 0
```

The bistable maintains its current state:

```text
Qt+1 = Qt
```

Therefore:

```text
Qt = 0 → Qt+1 = 0
Qt = 1 → Qt+1 = 1
```

---

### SET

```text
S = 1
R = 0
```

The next state is forced to:

```text
Qt+1 = 1
```

independently of the previous state.

```text
Qt = 0 → Qt+1 = 1
Qt = 1 → Qt+1 = 1
```

---

### RESET

```text
S = 0
R = 1
```

The next state is forced to:

```text
Qt+1 = 0
```

independently of the previous state.

```text
Qt = 0 → Qt+1 = 0
Qt = 1 → Qt+1 = 0
```

---

### Forbidden state

```text
S = 1
R = 1
```

This produces an anomalous condition:

```text
Q = 0
!Q = 0
```

The next state is not determinable because it depends on which input remains active longer.

Therefore:

```text
S = R = 1  → forbidden / undefined
```

---

## 5. SR State Table

```text
S R | Qt+1
----|-----
0 0 | Qt       → Hold
0 1 | 0        → Reset
1 0 | 1        → Set
1 1 | --       → Undefined
```

Key idea:

```text
00 → keep
01 → reset
10 → set
11 → forbidden
```

---

# 6. Timing Diagrams

Sequential circuits depend on time and past events, so their behavior is represented using a **timing diagram**.

A timing diagram has:

- horizontal axis → time
    
- vertical axis → logical values `0` and `1`
    

Bistables have a **switching delay**:

> The output changes after the corresponding input changes, not instantaneously.

If this delay is ignored, outputs would appear to switch instantaneously.

### Hazard / Glitch

A short unwanted transition on an input can produce unwanted behavior in an asynchronous bistable.

---

# 7. Clock Signal

In many circuits, the state must change only during specific time intervals.

A **clock** provides synchronization and defines when state transitions can occur.

The clock is:

- binary
    
- periodic
    
- characterized by a constant period.
    

### Period and frequency

```text
f = 1 / T
```

Example:

```text
T = 1 ns

f = 1 / 1 ns
  = 1 GHz
```

---

# 8. Synchronous SR Bistable

A synchronous SR bistable has:

- `S` → Set
    
- `R` → Reset
    
- `Clock`
    
- `Q` → state
    
- `!Q` → complement
    

Its behavior depends on the clock level.

### Clock = 0

The inputs are **ineffective**.

```text
Qt+1 = Qt
```

The bistable is **opaque**.

### Clock = 1

The inputs become effective.

The bistable is **transparent** and behaves like the asynchronous SR bistable.

---

## 9. Synchronous SR State Table

```text
C S R | Qt+1
------|-----
0 X X | Qt
1 0 0 | Qt
1 0 1 | 0
1 1 0 | 1
1 1 1 | --
```

Where:

```text
X = don't care
```

Interpretation:

```text
Clock = 0 → ignore S/R, keep state

Clock = 1:
    00 → Hold
    01 → Reset
    10 → Set
    11 → Undefined
```

---

# 10. Synchronous D Bistable

The D bistable simplifies the SR interface.

Inputs:

- `D` → data to be stored
    
- `Clock`
    

Outputs:

- `Q`
    
- `!Q`
    

### Clock = 0

`D` is ineffective.

```text
Qt+1 = Qt
```

The latch is **opaque**.

### Clock = 1

`D` is effective.

The bistable stores the value present on `D`:

```text
Qt+1 = D
```

---

## 11. Synchronous D State Table

```text
C D | Qt+1
----|-----
0 X | Qt
1 0 | 0
1 1 | 1
```

Therefore:

```text
Clock = 0 → Hold
Clock = 1 → Q follows D
```

---

# 12. Transparency

During the interval in which the clock is active, synchronous SR/D latches are **transparent**.

This means:

```text
Input changes
      ↓
State changes
      ↓
Output can change
```

Therefore, if an input changes multiple times or contains glitches, the output can also change multiple times.

### Design rule

During the transparency interval:

```text
Inputs must remain stable.
```

This avoids unwanted output transitions caused by glitches.

---

# 13. Latch vs Flip-Flop

This distinction is essential.

### Latch

The output/state can change according to the **level** of the control signal.

```text
Control active
      ↓
Input can affect output
```

Therefore, a latch is **level-sensitive**.

### Flip-flop

The output is updated on a **clock edge**.

```text
Clock edge
    ↓
Input sampled
    ↓
Output updated
```

Therefore, a flip-flop is **edge-sensitive**.

---

# 14. D Master-Slave Flip-Flop

To eliminate the transparency problem, a master-slave flip-flop uses **two synchronous D bistables connected in cascade**:

1. Master
    
2. Slave
    

Their clocks are inverted with respect to each other.

The result is that the output changes only at a clock edge.

---

## 15. Master-Slave Operation

### Clock HIGH

```text
Master → transparent
Slave  → opaque
```

The Master samples `D`.

The value is stored in:

```text
QM
```

### Clock LOW

```text
Master → opaque
Slave  → transparent
```

The Slave samples the Master's output and produces:

```text
QS
```

Therefore:

```text
Clock HIGH:
    D → Master

Clock LOW:
    Master → Slave → QS
```

---

## 16. Falling-Edge Master-Slave

For the configuration shown in the course:

```text
QM = master output
QS = slave output
```

The slave updates while the clock is low.

Therefore the final output changes on the **falling edge** of the clock.

```text
      ↓ active edge
CLK ────┐    ┌────
        └────┘
             ↑
           QS changes
```

A NOT symbol on the clock indicates this falling-edge behavior.

The active edge can instead be the rising edge depending on the clock convention.

---

# 17. Why Master-Slave Removes Transparency

The Master and Slave are never transparent at the same time.

```text
Clock HIGH:
    Master transparent
    Slave opaque

Clock LOW:
    Master opaque
    Slave transparent
```

Therefore, while the Slave is transparent, its input `QM` remains stable.

So:

```text
QM does not change
      ↓
QS cannot continuously follow D
      ↓
No output transparency
```

The output changes only when the clock changes phase.

---

# 18. Glitch Filtering

The Master-Slave structure can filter glitches on `D`.

During the active sampling interval, `D` must be stable according to the specified operating conditions.

Once the Master has sampled the input, `QM` remains unchanged during the Slave's transparent interval.

Therefore, glitches on `D` do not directly propagate to `QS`.

---

# 19. SR Master-Slave Flip-Flop

An SR master-slave flip-flop can also be constructed using:

- SR master latch
    
- SR slave latch
    
- inverted clock relationship
    

The same master-slave principle removes output transparency.

---

# 20. Two Ways to Classify Synchronous Bistables

The course distinguishes bistables according to **two independent relationships**.

## A. Input → State relationship

This determines **when the inputs modify the internal state**.

### Level-sensitive

The state can change during the entire interval in which the control signal is active.

```text
Control active
→ input changes can modify state
```

### Edge-sensitive

The state is updated only at a clock edge:

```text
Rising edge
or
Falling edge
```

---

## B. State → Output relationship

This determines **when the internal state updates the outputs**.

### Level-sensitive

During the active control interval:

```text
state changes
→ output changes
```

These elements are called **LATCHES**.

### Edge-sensitive

The outputs are updated on a clock edge.

These elements are called **FLIP-FLOPS**.

---

# 21. Latch vs Master-Slave vs Edge-Triggered

The course summary gives:

```text
Input → State     State → Output
--------------------------------
Level             Level      → Latch with clock
Level             Edge       → Master-Slave Flip-Flop
Edge              Edge       → Edge-Triggered Flip-Flop
```

Important:

### Latch with clock

```text
Input → State:  Level
State → Output: Level
```

### Master-Slave Flip-Flop

```text
Input → State:  Level
State → Output: Edge
```

### Edge-Triggered Flip-Flop

```text
Input → State:  Edge
State → Output: Edge
```

---

# 22. Edge-Triggered Flip-Flop

An edge-triggered flip-flop has a more complicated internal structure than a Master-Slave flip-flop, so the internal implementation is not studied in detail.

Its behavior is controlled by the **active edge**:

- rising edge
    
- falling edge
    

The active edge acts as a **sampling signal**.

At the active edge:

```text
Input
  ↓
Sample
  ↓
Store
  ↓
Present value at output
```

Therefore, the input does not need to remain stable for the entire active clock level as in a Master-Slave design.

---

# 23. Timing Requirements of Edge-Triggered Flip-Flops

For correct operation, the input must be stable around the active clock edge.

### Setup time

`T_set_up`

The minimum interval during which the input must remain stable **before the active edge**.

### Propagation time

`T_prop`

The time required for the newly stored state to become stable at the output.

### Combinational logic time

`T_comb`

The time required by the combinational logic between state elements.

The course gives:

```text
T_clock = T_prop + T_set_up + T_comb
```

Therefore, the clock period must be long enough for the complete operation to occur correctly.

---

# Exam Essentials

## Sequential circuits

```text
Sequential circuit:
Output depends on current inputs + past information.

State = stored information / memory.

Sequential circuit =
    Combinational Logic
    +
    Memory
    +
    Feedback
```

## SR asynchronous bistable

```text
S R | Qt+1
----|-----
0 0 | Qt       Hold
0 1 | 0        Reset
1 0 | 1        Set
1 1 | --       Forbidden
```

## Synchronous SR

```text
Clock = 0 → inputs ineffective → Hold

Clock = 1:
    S=0,R=0 → Hold
    S=0,R=1 → Reset
    S=1,R=0 → Set
    S=1,R=1 → Undefined
```

## Synchronous D

```text
Clock = 0 → Hold
Clock = 1 → Qt+1 = D
```

## Clock

```text
f = 1 / T
```

## Transparency

```text
Clock active
→ latch transparent
→ input changes can affect output
```

Therefore:

```text
Keep inputs stable during transparency.
```

## Master-Slave

```text
Clock HIGH:
    Master transparent
    Slave opaque

Clock LOW:
    Master opaque
    Slave transparent
```

For the falling-edge configuration:

```text
QS changes on falling edge.
```

## Critical distinctions

```text
LATCH:
    Input → State = Level
    State → Output = Level

MASTER-SLAVE:
    Input → State = Level
    State → Output = Edge

EDGE-TRIGGERED FF:
    Input → State = Edge
    State → Output = Edge
```

## Edge-triggered timing

```text
T_clock = T_prop + T_set_up + T_comb
```

Where:

```text
T_set_up = input stability before active edge
T_prop   = time for stored state to reach stable output
T_comb   = combinational logic delay
```