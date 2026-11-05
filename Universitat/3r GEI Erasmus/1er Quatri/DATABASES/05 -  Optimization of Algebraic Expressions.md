## 1. Equivalent Algebraic Expressions

Two relational algebra expressions are **equivalent** if they produce the **same result for all possible database instances**.

Example:

```
πSID,Name σTeacher="Meo" (Student ⋈ Exam ⋈ Course)
```

is equivalent to:

```
πSID,Name (Student ⋈(πSID (Exam ⋈ σTeacher="Meo" Course)))
```

Equivalent expressions can have very different **execution costs**.

---

## 2. Syntax Trees

Any relational algebra expression can be represented as a **tree**.

- Leaves → relations
    
- Internal nodes → relational algebra operators
    
- The tree represents the **order in which operators are applied**.
    

Example:

```
πNAME
   |
σDATE=10/09/20 ∧ GRADE=30
   |
STUDENT × EXAM
```

The syntax tree is useful for identifying transformations that can reduce execution cost.

---

# 3. Equivalence Transformations

The goal of algebraic optimization is to transform an expression into an **equivalent expression with lower execution cost**.

---

## 3.1 Elimination of Cartesian Products

A Cartesian product followed by a selection containing comparisons between attributes of the two relations can be replaced by a **join**.

![[Pasted image 20261006145232.png|536]]

This is valid when `P` is a conjunction of comparisons of the form:

```
ATTRIBUTE <comp> ATTRIBUTE
```

Example:

```
σstudent.SID=exam.SID (STUDENT × EXAM)
```

becomes:

```
STUDENT ⋈student.SID=exam.SID EXAM
```

This avoids generating the complete Cartesian product.

---

## 3.2 Push Selection Through Joins

A selection can be pushed below a join when its predicate only refers to attributes of one relation.

![[Pasted image 20261006145247.png|507]]

Condition:

```
P only refers to attributes of R
```

If the predicate contains conditions referring to different branches, they can be split and pushed independently.

Example:

```
σCity="Torino" (STUDENT ⋈ EXAM)
```

can become:

```
(σCity="Torino" STUDENT) ⋈ EXAM
```

### Why?

Filtering tuples **before** the join reduces the size of the intermediate result.

---

## 3.3 Push Projection Through Joins

Projection can also be pushed through a join.

![[Pasted image 20261006145313.png|480]]
where:

```
LR = L - SCHEMA(S) + JR
LS = L - SCHEMA(R) + JS
```

`JR` and `JS` are the attributes from `R` and `S` required by the join predicate `P`.

### Example

```
πSID,Name (STUDENT ⋈ EXAM)
```

can be transformed into:

```
(πSID,Name STUDENT) ⋈ (πSID EXAM)
```

Only attributes required for the final result and for the join are retained.

---

## 3.4 Idempotence of Selection

Applying selections successively can be combined into a single selection.

![[Pasted image 20261006145351.png|395]]

Example:

```
σCity="Torino" (σGrade=30 (R))
```

becomes:

```
σCity="Torino" ∧ Grade=30 (R)
```

This transformation can also be used in the opposite direction to create selections that can be pushed toward individual relations.

---

## 3.5 Idempotence of Projection

![[Pasted image 20261006145413.png|339]]

If `L2` contains all attributes in `L`:

```
L2 ⊇ L
```

then:

```
πL (πL2 (R))
    ↓
πL (R)
```

An unnecessary intermediate projection can therefore be eliminated.

---

## 3.6 Push Selection Through Union

Selection distributes over union:

![[Pasted image 20261006145435.png|452]]
Example:

```
σName="Paola" (ENGINEERS ∪ TURINERS)
```

becomes:

```
(σName="Paola" ENGINEERS)
∪
(σName="Paola" TURINERS)
```

---

## 3.7 Push Selection Through Difference

Selection also commutes with difference:

![[Pasted image 20261006145455.png|572]]

1. Better in space
2. Better in time

The same idea applies to intersection.

> **Selection commutes with all three set operators:**
> 
> - Union `∪`
>     
> - Difference `−`
>     
> - Intersection `∩`
>     

---

## 3.8 Push Projection Through Union

Projection distributes over union:

![[Pasted image 20261006145533.png|476]]

However:

> **Projection does NOT commute with difference or intersection.**

Example:

```
πCLIENT (ClientsA - ClientsB)
```

is **not** equivalent to:

```
πCLIENT(ClientsA) - πCLIENT(ClientsB)
```

Example from the slides:

```
πCLIENT (ClientsA - ClientsB)
= {Rossi, Verdi}
```

while:

```
πCLIENT ClientsA - πCLIENT ClientsB
= ∅
```

Therefore, projection cannot generally be pushed through `−` or `∩`.

---

## 3.9 Distribution of Join over Union

Join distributes over union:

![[Pasted image 20261006145642.png]]

This transformation can be useful when optimizing expressions involving unions.

---

# 4. Useful Algebraic Formulas

![[Pasted image 20261006150105.png]]

---

# 5. Algebraic Optimization

Among all equivalent representations, we prefer the one with the **minimum execution cost**.

The main informal criterion is:

> **Minimize the size of intermediate results.**

### Main optimization rules

1. **Push selections as much as possible**
    
    - Use transformations 2, 6 and 7.
        
2. **Push projections as much as possible**
    
    - Use transformations 3 and 8.
        
3. **Use idempotence**
    
    - Selection → transformation 4
        
    - Projection → transformation 5
        
4. **Eliminate Cartesian products**
    
    - Whenever a Cartesian product followed by a selection can become a join.
        

The general strategy is:

```
Filter early
↓
Keep only necessary attributes
↓
Replace × + selection with joins
↓
Minimize intermediate relations
↓
Produce final projection
```

---

# 6. Optimization Example

Given:

```
R(A,B,C)
S(C,D,E)
T(D,E,F,G)
```

Optimize:

```
πB
σ(R.C=S.C) ∧ (S.D=T.D) ∧ (R.A=1) ∧ (R.B>T.F)
(R × S × T)
```

### Step 1 — Eliminate Cartesian Products

Convert the relevant Cartesian products + selection predicates into joins.

```
R ⋈R.C=S.C S
```

and:

```
S ⋈S.D=T.D T
```

---

### Step 2 — Push Selections

`R.A=1` only concerns `R`:

```
σA=1(R)
```

`R.B>T.F` concerns `R` and `T`, so it must remain at their join.

---

### Step 3 — Push Projections

Only attributes needed later should be retained.

For example:

```
R → πB,C
S → πC,D
T → πD,F
```

because:

- `B` → final result
    
- `C` → join `R.C=S.C`
    
- `D` → join `S.D=T.D`
    
- `F` → condition `R.B>T.F`
    

---

### Optimized Structure

Conceptually:

```
              πB
               |
          ⋈ R.C=S.C
          /        \
    πB,C σA=1(R)   ⋈ S.D=T.D
                    /       \
                 πC,D     σD? / πD,F(T)
```

The exact tree follows the same optimization principles: **filter and project before expensive joins whenever possible**.

---

# 7. Reasoning About Predicates

Optimization does not only rely on automatic algebraic transformations.

We can also reason about predicates to detect **contradictions** or derive additional restrictions.

Example:

```
σ(S.A=R.A) ∧ (R.A>2) ∧ (S.A=1) (R × S)
```

Since:

```
S.A = R.A
```

and:

```
R.A > 2
```

and:

```
S.A = 1
```

we obtain a contradiction:

```
R.A = S.A = 1
```

but:

```
R.A > 2
```

Therefore:

```
Result = ∅
```

Recognizing this immediately is an important optimization.

---

# 8. Advanced Optimization Example

Given:

```
R(A,B,C)
S(C,D,E)
T(C,D,E)
```

Optimize:

```
πB
σ(R.C=S.C) ∧ (S.E=1) ∧ (R.B=2) ∧ (R.B>S.D)
(
    R × (S − T)
)
```

### Step 1 — Push selections

For `R`:

```
σB=2(R)
```

For `S`:

```
σE=1(S)
```

The predicate:

```
R.B>S.D
```

remains associated with the join because it uses attributes from both relations.

---

### Step 2 — Reason about predicates

Since:

```
R.B=2
```

and:

```
R.B>S.D
```

we can derive:

```
2 > S.D
```

therefore:

```
S.D < 2
```

So the selection on `S` can be strengthened:

```
σE=1 ∧ D<2(S)
```

This reduces the amount of data before the difference/join operations.

---

### Step 3 — Push projections

Keep only attributes required later:

```
R → πB,C σB=2(R)
```

For `S`:

```
S → πC,D σE=1 ∧ D<2(S)
```

For `T`:

```
T → πC,D(T)
```

---

# 9. Optimization Strategy

When optimizing a relational algebra expression:

```
1. Build the syntax tree
2. Identify selections
3. Push selections toward the leaves
4. Split conjunctive predicates when possible
5. Identify Cartesian products followed by comparisons
6. Replace them with joins
7. Push projections toward the leaves
8. Keep attributes required for joins/selections
9. Use idempotence
10. Reason about predicates to derive additional restrictions
11. Remove unnecessary operations
12. Keep the final projection as late as necessary
```

### Main objective

```
Equivalent result
+
Smaller intermediate relations
=
Lower execution cost
```

---

# Exam Essentials

### Equivalence

```
Equivalent expressions
→ same result for every possible database instance
```

### Main transformations

```
σP(R × S) → R ⋈P S
```

```
σP(R ⋈ S) → (σP R) ⋈ S
```

when `P` only refers to `R`.

```
πL(R ∪ S) → πL(R) ∪ πL(S)
```

```
σP(R ∪ S) → σP(R) ∪ σP(S)
```

```
σP(R − S) → σP(R) − σP(S)
```

### Important non-equivalence

```
πL(R − S) ≠ πL(R) − πL(S)
```

in general.

Likewise, projection does not generally commute with:

```
−
∩
```

### Selection idempotence

```
σP1(σP2(R)) = σP1∧P2(R)
```

### Optimization rule

> **Push selections and projections as close to the leaves as possible, minimize intermediate results, and replace Cartesian products + suitable selections with joins.**

### Predicate reasoning

Always check whether predicates imply:

```
Contradiction → ∅
```

or allow additional restrictions to be derived.