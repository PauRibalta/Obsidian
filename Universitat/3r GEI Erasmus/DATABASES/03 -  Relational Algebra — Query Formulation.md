## 1. Query Languages

For the relational data model:
### Commercial languages

- SQL
- QBE (Query By Example)

### Formal languages

- Relational Algebra
- Relational Calculus
- Logic programming / Datalog

---

# 2. Relational Algebra

Relational Algebra provides operators for querying relations.

Main operators:

- Selection: Filters tuples based on a predicate
- Projection: Prunes out columns that are not interesting (and eliminates duplicates)
- Redenomination: Give (new) names to relations or to their attributes
- Cartesian product: Combines the items of two relations in all possible ways
- Union, Difference and Intersection: Perform set operations of two relations (that have the same schema)
- Join: Just a name for a Selection over a Cartesian Product

![[Pasted image 20260918175046.png|610]]

Relational Algebra is a **closed algebra**:

> Every operator generates a relation, so expressions can be composed.

---

# 3. Selection 

Symbol and Syntax:

```text
σ predicate RELATION
```

Selection:

- keeps the **same schema**
- keeps only tuples satisfying the predicate

Example:

```text
σ Name='Paola' STUDENT
```

→ returns only the tuple(s) satisfying `Name = 'Paola'`.

---

## 4. Selection Predicates 

Predicates are Boolean expressions.

### Boolean operators

- AND → ∧ 
- OR → ∨ 
- NOT → ¬ 
### Comparators

=, !=, <, >, <=, >=
### Terms

- constants
- attributes
- arithmetic expressions of terms

A simple predicate has the form:

```text
term comparator term
```

---

# 5. Projection

Symbol and Syntax:

```text
Π att1, ..., attN RELATION
```

Projection:

- keeps only the mentioned attributes
- removes the other columns
- creates a relation with the selected attributes

Example:

```text
Π Name, Dept STUDENT
```

---

## 6. Projection and Duplicates

In the **formal relational model**:
- projection eliminates duplicates

In real systems:
- duplicate elimination must be explicitly requested.

---

# 7. Union 

Symbol and Syntax:

```text
R ∪ S
```

Produces the union of two relations.

Relations must be **compatible**.

Formally:
- same schema

In real systems, the requirement may be relaxed to:

- same degree
- their domains have the same types

---

# 8. Difference and Intersection

### Difference

R−S: Returns tuples belonging to R but not S.
### Intersection

R∩S:  Returns tuples belonging to both relations.

They have the same schema-compatibility requirements as union.

---

# 9. Cartesian Product 

Symbol and Syntax:

```text
R × S
```

Combines every tuple of R with every tuple of S.
### Schema

- Contains all attributes from both relations.
- degree(R×S)=degree(R)+degree(S)
### Instance

- Contains all possible pairs of tuples of R and of S, juxtaposed
- cardinality(R×S)=cardinality(R)×cardinality(S)

If both relations contain attributes with the same name, use table prefixes:

R(A,B,C) S(C,D) -> RxS( A, B, R.C, S.C, D )

---

# 10. Join 

A join combines tuples from two relations according to a predicate.

Symbol and Syntax:

```text
R ⋈predicate S
```

Definition:

R⋈pS=σp(R×S) where p is the predicate

As a result it generates a relation (with no name) where 
The schema is: 
- The concatenation of the schemas of the tables
The instance has: 
- The tuples of the Cartesian product that fulfill the selection predicate

Therefore:

> **Join = Selection over a Cartesian Product**

The resulting schema is the concatenation of the two schemas.

---

# 11. Join Predicate 

A join predicate consists of conjunctive simple predicates:

```text
R ⋈ attr1 comp attr2 S
```

where:

- `attr1` belongs to `R`
- `attr2` belongs to `S`
- comparator is one of:

```text
= != < <= > >=
```

### Equi-join

A join whose predicate uses only the comparator =.

---

# 12. Natural Join 

Natural join is useful when corresponding attributes have the same name.

Syntax:

```text
R ⋈ S
```

It:

- matches homonymous attributes
- implies equality predicates
- does not replicate the matching column

Example:

```text
STUDENT ⋈ EXAM
```

implicitly matches the common attributes.

---

# 13. Join of Multiple Relations

Joins can be chained:

```text
STUDENT ⋈ EXAM ⋈ COURSE
```

Equivalent to:

```text
(STUDENT ⋈ EXAM) ⋈ COURSE
```

or:

```text
STUDENT ⋈ (EXAM ⋈ COURSE)
```

This allows information from several relations to be combined.

---

# 14. Assignment

Symbol and Syntax

```text
CScientists := σ Dept='CS' STUDENT
```

Assignment is not exactly an algebraic operator.

---

# 15. Redenomination 

Symbol:

ρ\boxed{\rho}

Used to change attribute names.

Symbol and Syntax:

```text
ρ PlaceOfBirth ← City STUDENT
```

Redenomination is particularly important when joining a relation **with itself**.

Why?

Because both copies have the same relation/attribute names, so prefixes alone are insufficient.

---

# 16. Redenomination + Union

Redenomination can make formally different relations compatible.

Example:
![[Pasted image 20260918180934.png]]
The common attribute name makes the relations compatible for union.

---

# 17. Equivalence of Expressions 

The same query can often be expressed using different relational algebra expressions.

Example:

```text
Π Name σ Grade=30 ∧ Title='Math'(STUDENT ⋈ EXAM ⋈ COURSE)
```

Equivalent to pushing selections closer to the relevant relations:

```text
Π Name(STUDENT ⋈(σ Grade=30 EXAM⋈(σ Title='Math' COURSE)))
```

Important idea:

> Different algebraic expressions can produce the same result.

---

# 18. Queries with Multiple Occurrences

![[Pasted image 20260918181243.png|554]]

This technique allows the same relation to be compared with itself.

---

# 19. At Least N Occurrences 

Chains of joins can express:

> **at least N occurrences** of a fact.

Example:

- at least 2 exams → 2 copies of `EXAM`
- at least 3 exams → 3 copies
- at least 4 exams → 4 copies

Different copies are identified through redenomination and joined using the relevant attributes.

---

# 20. Exactly N Occurrences

A chain of joins naturally expresses **at least N**, not exactly N.

To express:

> exactly 3 exams

use:

AtLeast3 − AtLeast4

Then join with `STUDENT` to obtain the names.

![[Pasted image 20260922152138.png]]

---

# 21. Difference for "Never" 

To find students who never passed any exam:

```text
Π Name(STUDENT ⋈(Π SId STUDENT - Π SId EXAM))
```

General idea:

Never=All−Those who did it

---

# 22. "Always" Conditions 

To find students who always got more than 28:

1. Find students who took at least one exam.
2. Find students who got `Grade <= 28`.
3. Subtract the second set from the first.
4. Finally, join those SIds with STUDENT to extract their names

Conceptually:

Always >28=AtLeastOne−AtLeastOne Bad

![[Pasted image 20260922153447.png|523]]

---

# 23. "Always OR Never"

A query can combine conditions using union.

Example:

> students who either always got more than 28 or never took an exam

Conceptually:

```text
(always > 28) ∪ (never examined)
```

![[Pasted image 20260922153511.png|524]]

---

# 24. Same Student + Same Day

To find students who passed two different courses on the same day:

1. Build the list of exams for the first course.
2. Build the list of exams for the second course.
3. Rename one copy.
4. Join using:
    - same student
    - same date
5. Join with `STUDENT` to obtain names.

Example:

```text
SId = SId'
AND
Date = Date'
```

![[Pasted image 20260922153941.png|484]]

---

# 25. Finding the First Exam 

Idea:

> Every exam that is not the first one has another exam before it.

Steps:

1. Find exams that have a previous exam.
2. Subtract them from all exams.
3. Remaining exams = first exam(s).

![[Pasted image 20260922154915.png]]

We could use EXAM' and date'. The join in this query is not an equijoin as we are not looking for an equality.

---

# 26. First Exam of Each Student

Same idea, but the previous exam must belong to the **same student**.

Condition:

```text
Date > Date'
AND
SId = SId'
```

Then:

```text
All exams - Exams preceded by another exam of the same student
```

→ first exam(s) of each student.

![[Pasted image 20260922155311.png]]

We can use EXAM', SId' and Date' here as well.

---

# 27. Query Strategy

For complex queries, break the problem into smaller relations.

Typical strategy:

1. **Select** relevant tuples.
2. **Join** related information.
3. **Project** required attributes.
4. Use **difference** for "never" / exclusion.
5. Use **union** for alternatives.
6. Use **redenomination** when comparing a relation with itself.
7. Use **assignment** to name intermediate results.

---

# Exam Essentials

## Operators

- Selection → σ
- Projection → Π
- Union → ∪
- Difference → -
- Intersection → ∩
- Cartesian product → ×
- Join → ⋈
- Redenomination → ρ
- Assignment → `:=`

## What Each Operator Does

- **Selection** → filters **rows/tuples**
- **Projection** → selects **columns/attributes**
- **Union** → combines tuples from two compatible relations
- **Difference** → tuples in first relation but not second
- **Intersection** → tuples in both relations
- **Cartesian product** → every tuple of RR with every tuple of SS
- **Join** → combines relations according to a predicate
- **Redenomination** → changes attribute names
- **Assignment** → gives a result a name

## Formulas to Know 

**Selection**

```text
σ predicate R
```

**Projection**

```text
Π attributes R
```

**Cartesian product**

```text
R × S
```

degree(R×S)=degree(R)+degree(S)
cardinality(R×S)=cardinality(R)×cardinality(S)c

**Join**

R⋈pS=σp(R×S)

**Never**

```text
All - Those who did it
```

**Always**

```text
Those who did it at least once
-
Those who violated the condition
```

## Key Things to Remember

- Relational Algebra is a **closed algebra**
    
- Every operator produces a **relation**
    
- Selection → **rows**
    
- Projection → **columns**
    
- Projection eliminates duplicates in the **formal model**
    
- Union/difference/intersection require **compatible schemas**
    
- Join = **selection over Cartesian product**
    
- Equi-join → join predicate uses only `=`
    
- Natural join → automatically matches same-named attributes
    
- `ρ` is essential for **self-joins**
    
- Chains of joins can express **at least N**
    
- **Exactly N** → `at least N − at least N+1`
    
- **Never** → use **difference**
    
- **Always** → find violations and subtract them
    
- Complex queries → break them into intermediate relations