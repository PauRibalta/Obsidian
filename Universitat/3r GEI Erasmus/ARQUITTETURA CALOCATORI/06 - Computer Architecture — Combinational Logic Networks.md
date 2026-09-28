## 1. CMOS Technology and Transistors

### CMOS

**CMOS** = Complementary Metal Oxide Semiconductors.

- Transistors are used to design elementary logic gates.
    
- CMOS technology uses:
    
    - **pMOS** (p-type transistor)
        
    - **nMOS** (n-type transistor)
        
- pMOS and nMOS operate complementarily.
    

### Transistor as a Switch

A transistor behaves approximately as an **ON/OFF switch**.

- **nMOS:** drain-source circuit closes when the gate is at `3V`.
    
- **pMOS:** drain-source circuit closes when the gate is at `0V`.
    

### Pull-up / Pull-down

For a simple CMOS circuit with input `A` and output `Y`:

- `A = 0`:
    
    - pMOS is ON
        
    - nMOS is OFF
        
    - output is connected to `3V`
        
    - **Y = 1**
        
    - pMOS performs a **pull-up** function.
        
- `A = 1`:
    
    - pMOS is OFF
        
    - nMOS is ON
        
    - output is connected to `0V`
        
    - **Y = 0**
        
    - nMOS performs a **pull-down** function.
        

### CMOS NOT Gate

Logical interpretation:

```text
0V → logic 0
3V → logic 1
```

The CMOS inverter implements:

```text
Y = !A
```

Truth table:

```text
A | Y
--|--
0 | 1
1 | 0
```

---

# 2. Boolean Algebra

## Boolean Algebra

Boolean algebra describes logical operations.

It consists of:

- Set `B` of Boolean operands.
    
- Boolean operators acting on the operands.
    
- Neutral elements used to define the operations.
    

### Switching Algebra

For digital circuits:

```text
B = {0, 1}
```

where:

```text
0 = false
1 = true
```

A **truth table** lists all possible input combinations and the corresponding output.

For `n` inputs:

```text
Number of input combinations = 2^n
```

Elementary Boolean operators:

- `NOT`
    
- `AND`
    
- `OR`
    

---

# 3. Elementary Logic Gates

![[Pasted image 20260928144924.png]]

---

# 4. Boolean Algebra Properties

Boolean expressions describe a circuit as:

```text
U = f(I)
```

Starting from a Boolean equation, a corresponding logic circuit can be derived.

## Operator Precedence

```text
1. NOT
2. AND
3. OR
```

---

## Fundamental Properties

|Property|AND|OR|
|---|---|---|
|Identity|`1 • A = A`|`0 + A = A`|
|Null element|`0 • A = 0`|`1 + A = 1`|
|Idempotence|`A • A = A`|`A + A = A`|
|Inverse|`A • !A = 0`|`A + !A = 1`|
|Commutative|`A • B = B • A`|`A + B = B + A`|
|Associative|`(A • B) • C = A • (B • C)`|`(A + B) + C = A + (B + C)`|
|Distributive|`A • (B + C) = A•B + A•C`|`A + B•C = (A+B)•(A+C)`|
|Absorption|`A • (A + B) = A`|`A + A•B = A`|

---

## Principle of Duality

The **dual** of a Boolean function is obtained by replacing:

```text
AND ↔ OR
0 ↔ 1
```

For example:

```text
1 • A = A
```

has the dual:

```text
0 + A = A
```

---

## Equivalence

Two Boolean expressions are **equivalent** if they have the same truth table.

Example:

```text
A + A•B = A
```

The expressions may look different but produce the same output for every input combination.

---

# 5. De Morgan's Theorems

### First De Morgan theorem

```text
!(A • B) = !A + !B
```

Meaning:

> NAND = OR of the negated variables.

Equivalent form:

```text
A • B = !(!A + !B)
```

---

### Second De Morgan theorem

```text
!(A + B) = !A • !B
```

Meaning:

> NOR = AND of the negated variables.

Equivalent form:

```text
A + B = !(!A • !B)
```

---

# 6. XOR and XNOR Expressions

### XOR

```text
A Å B = !A•B + A•!B
```

XOR is `1` when exactly one input is `1`.

### XNOR

```text
!(A Å B) = !A•!B + A•B
```

XNOR is `1` when both inputs are equal.

---

# 7. Boolean Expression Simplification

Boolean expressions can be simplified by applying Boolean algebra properties.

Example:

```text
A + A•B
```

Using absorption:

```text
A + A•B = A
```

Another example:

```text
A•B + A•!B
```

```text
= A•(B + !B)
= A•1
= A
```

### Example

Given:

```text
f(A,B,C) = !A!BC + !ABC + !AB!C
```

Simplification:

```text
= !AC(!B + B) + !AB(C + !C)
= !AC + !AB
= !A(C + B)
```

The simplified expression implements the same Boolean function with a different circuit.

---

# 8. Functionally Complete Sets

A set of operators is **functionally complete** if any Boolean function can be represented using only operators from that set.

## AND, OR, NOT

```text
{AND, OR, NOT}
```

is functionally complete.

---

## NOT + AND

Using De Morgan:

```text
!(!A • !B) = A + B
```

Therefore:

```text
{NOT, AND}
```

is functionally complete.

---

## NOT + OR

Using De Morgan:

```text
!(!A + !B) = A • B
```

Therefore:

```text
{NOT, OR}
```

is functionally complete.

---

## AND + OR

```text
{AND, OR}
```

is **not** functionally complete because NOT cannot be obtained by combining only AND and OR.

---

# 9. Universal Operators

## NOR

NOR alone is functionally complete.

NOT:

```text
A NOR A = !A
```

OR:

```text
(A NOR B) NOR 0 = A + B
```

AND:

```text
(A NOR 0) NOR (B NOR 0) = A • B
```

Therefore:

```text
NOR = universal operator
```

---

## NAND

NAND alone is also functionally complete.

NOT:

```text
A NAND A = !A
```

AND:

```text
(A NAND B) NAND 1 = A • B
```

OR:

```text
(A NAND 1) NAND (B NAND 1) = A + B
```

Therefore:

```text
NAND = universal operator
```

---

# 10. Multi-Input Logic Gates

Some 2-input gates can be generalized to 3, 4 or more inputs.

Most commonly:

- AND
    
- OR
    

Typical implementations have:

```text
2, 4 or 8 inputs
```

### Multi-input AND

```text
X = A • B • C
```

Output is `1` only if **all inputs are 1**.

A 3-input AND can be constructed by cascading 2-input AND gates.

---

## Multi-input XOR

For `n` inputs:

```text
XOR = 1 ⇔ number of 1s is odd
```

## Multi-input XNOR

For `n` inputs:

```text
XNOR = 1 ⇔ number of 1s is even
```

---

# 11. Cost and Propagation Delay

A multi-input gate can be implemented in different structures.

Example: 4-input AND.

### Cascade structure

```text
Cost = 3 AND gates
Delay = 3 ns
```

### Tree structure

```text
Cost = 3 AND gates
Delay = 2 ns
```

Both implementations use the same number of gates, but the tree has a smaller propagation delay.

### Important

**Cost** and **propagation delay** are independent design criteria.

A circuit with the same logical function can have a different:

- number of gates
    
- propagation delay
    
- critical path
    
- operating frequency
    

---

# 12. Combinational Logic Networks

A **combinational network** is a digital logic circuit whose output depends only on the current input combination.

Characteristics:

- `n ≥ 1` inputs.
    
- Built from interconnected logic gates.
    
- Output corresponds to a Boolean function.
    
- **No dependence on previous circuit history.**
    
- Output changes after an input change, ignoring propagation delay.
    

A combinational network can be represented equivalently as:

```text
Truth table
      ↕
Boolean expression
      ↕
Logic circuit
```

---

# 13. From Boolean Function to Circuit

Example:

```text
F(A,B,C) = A•B + !C
```

Implementation:

1. AND gate computes:
    

```text
P = A•B
```

2. NOT gate computes:
    

```text
Q = !C
```

3. OR gate computes:
    

```text
F = P + Q
```

Therefore:

```text
A,B,C
  ↓
logic gates
  ↓
F
```

Internal signals correspond to intermediate Boolean expressions.

---

# 14. From Circuit to Boolean Function

The reverse process is also possible.

For each internal signal:

1. Identify the operation performed by the gate.
    
2. Write the corresponding Boolean expression.
    
3. Substitute intermediate expressions until reaching the output.
    

Example:

```text
P = A•B
Q = !C
F = P + Q
```

Therefore:

```text
F = A•B + !C
```

---

# 15. Circuit Simulation and Truth Tables

A truth table for a combinational network can be obtained by simulating the circuit.

For `n` inputs:

```text
Number of simulations = 2^n
```

Example:

```text
n = 3

2^3 = 8 simulations
```

Each possible input combination is propagated through the circuit to determine the output.

---

# 16. Equivalent Combinational Networks

A Boolean function can be implemented by multiple different combinational networks.

Two networks are **equivalent** if they implement the same Boolean function:

```text
same truth table
```

However, their circuit structures may differ.

Therefore, equivalent networks can have different:

- cost
    
- propagation delay
    
- critical path
    

### Example

```text
F1 = AB + AC
```

Using distributive property:

```text
F1 = A(B + C)
```

Therefore:

```text
F2 = A(B + C)
```

and:

```text
F1 ≡ F2
```

But their circuits have different structures.

### Circuit comparison

For `F1 = AB + AC`:

```text
Cost = 3 elementary gates
Delay = 2 ns
Frequency = 1 / 2 ns = 500 MHz
```

For `F2 = A(B + C)`:

```text
Cost = 2 elementary gates
Delay = 2 ns
Frequency = 1 / 2 ns = 500 MHz
```

So the equivalent implementation can have a lower circuit cost while maintaining the same delay.

---

# 17. Propagation Delay and Critical Path

The **propagation delay** of a combinational network is determined by the longest path from an input to the output.

This is the **critical path**.

Example:

```text
Critical path delay = 5 ns
```

Then:

```text
Switching frequency = 1 / 5 ns = 200 MHz
```

In general:

```text
Maximum frequency = 1 / critical-path delay
```

---

# 18. Synthesis of Combinational Networks

**Synthesis** starts from a truth table and produces a logic circuit implementing the corresponding Boolean function.

For a given truth table:

- Multiple equivalent networks may exist.
    
- The synthesis solution is therefore not necessarily unique.
    
- Different synthesis procedures may differ in:
    
    - complexity
        
    - resulting circuit cost
        
    - propagation delay
        

The slides introduce two canonical forms:

```text
1. Sum of Products (SoP)
2. Product of Sums (PoS)
```

For a given Boolean function there is exactly one canonical SoP and one canonical PoS representation.

---

# 19. First Canonical Form — Sum of Products (SoP)

**SoP = Sum of Products**

The function is expressed as:

```text
OR of AND terms
```

To construct the canonical SoP from a truth table:

1. Select all rows where:
    

```text
F = 1
```

2. For each selected row, create a **minterm**.
    
3. OR all minterms together.
    

---

## Minterm

A **minterm** `mi` is a Boolean function that is `1` for exactly one input configuration.

For each input variable:

- If the corresponding input value is `1` → use the variable normally.
    
- If the corresponding input value is `0` → use the complemented variable.
    

For `n` inputs:

```text
Maximum number of minterms = 2^n
```

Each minterm is an AND of `n` literals.

### Example

For:

```text
A = 0
B = 1
```

the corresponding minterm is:

```text
m1 = !A•B
```

---

# 20. SoP Example

Given:

```text
a b | f
----|--
0 0 | 0
0 1 | 1
1 0 | 0
1 1 | 1
```

Rows where `f = 1`:

```text
01
11
```

Corresponding minterms:

```text
m1 = !a•b
m3 = a•b
```

Therefore:

```text
f(a,b) = !a•b + a•b
```

This is the **canonical SoP**.

---

# 21. Majority Function — SoP

Consider a 3-input majority function:

```text
F = 1 ⇔ at least two inputs are 1
```

Truth table:

```text
A B C | F
------|--
0 0 0 | 0
0 0 1 | 0
0 1 0 | 0
0 1 1 | 1
1 0 0 | 0
1 0 1 | 1
1 1 0 | 1
1 1 1 | 1
```

Rows with `F = 1` give:

```text
m3 = !A•B•C
m5 = A•!B•C
m6 = A•B•!C
m7 = A•B•C
```

Therefore:

```text
F(A,B,C) =
!A•B•C
+ A•!B•C
+ A•B•!C
+ A•B•C
```

This is the canonical **SoP**.

It produces a 2-level network:

```text
AND level → OR level
```

---

# 22. Simplifying Canonical SoP

Canonical SoP can often be simplified.

Example:

```text
F =
!ABC + A!BC + AB!C + ABC
```

Using Boolean properties:

```text
= BC(!A + A) + AC(!B + B) + AB(!C + C)
= BC + AC + AB
```

Therefore:

```text
F = AB + AC + BC
```

The simplified expression implements the same function with fewer logic operations.

---

# 23. Second Canonical Form — Product of Sums (PoS)

**PoS = Product of Sums**

The function is expressed as:

```text
AND of OR terms
```

To construct the canonical PoS from a truth table:

1. Select all rows where:
    

```text
F = 0
```

2. For each selected row, create a **maxterm**.
    
3. AND all maxterms together.
    

---

## Maxterm

A **maxterm** `Mi` is a Boolean function that is `0` for exactly one input configuration.

For each input variable:

- If the corresponding input value is `0` → use the variable normally.
    
- If the corresponding input value is `1` → use the complemented variable.
    

Each maxterm is an OR of `n` literals.

---

# 24. PoS Example

Given:

```text
a b | f
----|--
0 0 | 0
0 1 | 1
1 0 | 0
1 1 | 1
```

Rows where `f = 0`:

```text
00
10
```

Corresponding maxterms:

```text
M0 = a + b
M2 = !a + b
```

Therefore:

```text
f(a,b) = (a + b) • (!a + b)
```

This is the canonical **PoS**.

---

# 25. Majority Function — PoS

Using the same majority function:

```text
F = 1 ⇔ at least two inputs are 1
```

The rows where:

```text
F = 0
```

are:

```text
000
001
010
100
```

Therefore:

```text
F =
(A + B + C)
• (A + B + !C)
• (A + !B + C)
• (!A + B + C)
```

This is the canonical **PoS** representation.

---

# 26. SoP vs PoS

### SoP

Built from rows where:

```text
F = 1
```

Uses:

```text
Minterms
AND → OR
```

Canonical structure:

```text
AND gates → OR gate
```

### PoS

Built from rows where:

```text
F = 0
```

Uses:

```text
Maxterms
OR → AND
```

Canonical structure:

```text
OR gates → AND gate
```

---

# Exam Essentials

## CMOS

```text
pMOS → pull-up → output connected to 3V
nMOS → pull-down → output connected to 0V
```

```text
A = 0 → Y = 1
A = 1 → Y = 0
```

---

## Logic Gates

```text
NOT:  X = !A

AND:  X = A•B

OR:   X = A+B

NAND: X = !(A•B)

NOR:  X = !(A+B)

XOR:  X = A Å B

XNOR: X = !(A Å B)
```

Remember:

```text
XOR  = 1 when inputs are different
XNOR = 1 when inputs are equal
```

---

## Boolean Properties

Know:

```text
1•A = A
0•A = 0
A•A = A
A•!A = 0

0+A = A
1+A = 1
A+A = A
A+!A = 1
```

```text
A+B = B+A
A•B = B•A
```

```text
(A+B)+C = A+(B+C)
(A•B)•C = A•(B•C)
```

```text
A•(B+C) = A•B + A•C

A+B•C = (A+B)•(A+C)
```

```text
A + A•B = A
A•(A+B) = A
```

---

## De Morgan

```text
!(A•B) = !A + !B
!(A+B) = !A•!B
```

---

## Functional Completeness

```text
{AND, OR, NOT} → complete

{NOT, AND} → complete

{NOT, OR} → complete

{AND, OR} → NOT complete

{NAND} → universal

{NOR} → universal
```

---

## Combinational Networks

```text
Output = function(current inputs)
```

No dependence on previous history.

Equivalent representations:

```text
Truth table ↔ Boolean expression ↔ Logic circuit
```

For `n` inputs:

```text
Number of truth-table rows = 2^n
```

---

## Critical Path

```text
Propagation delay = delay of longest input-to-output path
```

```text
Maximum frequency = 1 / critical-path delay
```

---

## Canonical Forms

### SoP

Use rows where:

```text
F = 1
```

Then:

```text
F = OR of minterms
```

Minterm rule:

```text
input = 1 → x
input = 0 → !x
```

---

### PoS

Use rows where:

```text
F = 0
```

Then:

```text
F = AND of maxterms
```

Maxterm rule:

```text
input = 0 → x
input = 1 → !x
```

### Key distinction

```text
SoP → 1s → minterms → AND then OR

PoS → 0s → maxterms → OR then AND
```