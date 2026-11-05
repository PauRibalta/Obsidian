## 1. From Relational Algebra to Datalog

Relational Algebra has important limitations:

- Cannot derive new values through arithmetic or unit conversions.
- Cannot perform aggregates such as `COUNT` or `AVG`.
- Has existential quantification and negation.
- Cannot express **recursion** or the **transitive closure** of a relation.

Example: finding all hierarchical relationships such as all bosses of an employee requires a new join for every level of the hierarchy.

**Datalog** addresses these limitations through **logic-based rules** and recursion.

---

## 2. Datalog

Datalog is a logic-based query language:

- A kind of **"Prolog for databases"**.
- Based on **rules**.
- Rules define new **views** over existing data.
- New knowledge is inferred from known facts.

A Datalog program is a **set of rules**.

---

## 3. Predicates, Literals and Rules

A **predicate/literal** has:

- a name
- a list of arguments

Arguments can be:

- **Constants**
- **Variables**
- **Don't-care variable** `_`

Example:

```text
Student(X, _, "Milan", _)
```

A rule has a **head** and a **body**:

```text
Head :- Body1, Body2, ..., BodyN
```

Example:

```text
Father(X,Y) :- Person(X, _, 'M'), Parenthood(X,Y)
```

Interpretation:

> If the body is true, then the head is also true.

### Safety rule

All variables occurring in the head must occur in at least one literal of the body.

---

## 4. Facts and Ground Literals

Each tuple in the database represents a basic **fact**, also called a **ground literal**.

A ground literal contains only constants.

Examples:

```text
Parenthood("Carlo", "Antonio")
Person("Carlo", "65", "M")
```

A ground literal is:

- **true** if the corresponding tuple exists in the database
- **false** otherwise

---

## 5. Rule Evaluation and Unification

A rule is true when the literals in its body can be consistently **unified**.

Example:

```text
Father(X,Y) :- Person(X, _, 'M'), Parenthood(X,Y)
```

Possible values of `X` come from matching:

```text
Person(X, _, 'M')
```

and possible `(X,Y)` pairs come from:

```text
Parenthood(X,Y)
```

Only combinations where the variable assignments are consistent produce valid results.

### Unification

**Unification** = assigning variables with consistent constant values taken from database facts.

---

## 6. Comparisons and Functions

The body of a rule can contain comparison predicates:

```text
≠   <   ≤   >   ≥
```

`=` is normally expressed through unification.

Example:

```text
Adult(N) :- Person(N,A,_), A >= 18
```

Functions are also allowed in rule heads.

The course restricts these to arithmetic operators:

```text
+   -   *   /
```

Example:

```text
AgeNextYear(N,A+1) :- Person(N,A,_)
```

---

## 7. Datalog and Relational Algebra

Example:

```text
Father(X,Y) :- Person(X, _, 'M'), Parenthood(X,Y)
```

Corresponding Relational Algebra:

```text
Father := Π1,5 σ3="M" (PERSON ⋈1=4 PARENTHOOD)
```

The correspondence is based on the positions of variables in the body:

![[Pasted image 20260930112311.png|533]]
- selection → `σ`
- join → `⋈`
- projection → `Π`

---

## 8. EDB and IDB

### Extensional Database — EDB

The **basic facts** stored in the database.

- Necessarily stored somewhere.
- Correspond to the original database tuples.

### Intensional Database — IDB

Knowledge that can be **inferred from the EDB**.

- Defined by rule heads.
- May be generated/computed only when necessary.

Normally:

```text
EDB ∩ IDB = ∅
```

---

# 9. Queries in Datalog

A query is expressed as a **goal**.

Example:

```text
?- Parent("Anna", X)
```

All possible unifications for `X` are attempted.

If:

```text
Parenthood("Anna","Antonio")
Parenthood("Anna","Gianni")
```

then:

```text
X = "Antonio"
X = "Gianni"
```

A goal without variables returns `True` or `False`.

```text
?- Parenthood("Anna","Antonio")   → True

?- Parenthood("Anna","Andrea")    → False
```

Queries can use both:

- EDB facts
- IDB rules

Example:

```text
?- Father(X,"Gianni")
```

returns:

```text
X = "Carlo"
```

---

# 10. Complex Queries

Complex queries often require an ad-hoc rule.

Example: find Antonio's brothers.

First define:

```text
Brother(X,Y) :-
    Parenthood(Z,X),
    Parenthood(Z,Y),
    Person(Y,_, 'M'),
    X ≠ Y
```

Then query:

```text
?- Brother("Antonio",Y)
```

Result:

```text
Y = "Gianni"
```

---

# 11. Expressiveness

Datalog **without negation** can express:

- Selection `σ`
- Projection `Π`
- Cartesian product `×`
- Union `∪`

### Union

Multiple rules with the same head:

```text
P(X,Y) :- R(X,Y).
P(X,Y) :- S(X,Y).
```

Equivalent to:

```text
P = R ∪ S
```

### Difference

Difference can be expressed using negation:

```text
P(X,Y) :- R(X,Y), ¬S(X,Y)
```

Equivalent to:

```text
P = R − S
```

Negation must be used carefully.

---

# 12. Negation

Literals in a rule body can be negated:

```text
¬R(X)
```

Negation increases expressive power but can cause:

- nontermination
- unsafe rules
- incorrect results when combined with recursion

### Unsafe negation

Example:

```text
S(X) :- ¬R(X)
```

This is **not safe**.

A safe version is:

```text
S(X) :- ¬R(X), P(X)
```

### Safety rule

Every variable occurring in a negated literal must also occur in a **positive literal** in the body.
![[Pasted image 20260930112207.png|426]]

---

# 13. Recursion

A recursive rule reuses its own head predicate in its body.

Example:

```text
Descendant(X,Y) :- Parenthood(X,Y)

Descendant(X,Y) :-Descendant(X,Z), Parenthood(Z,Y)
```

The two parts are:

- **Base case** → direct parent-child relationship
- **Inductive/recursive step** → extend an already known descendant relationship

Recursion allows Datalog to express **transitive closure**.

---

# 14. Recursive Evaluation

Recursive rules are evaluated iteratively.

At each iteration:

1. Variables are unified.
2. New results are generated.
3. New results are added to previously discovered results.
4. The process continues until no new knowledge can be added.

For `Descendant`:

```text
Descendant0 = PARENTHOOD
```

Then:

```text
Descendant1 = Descendant0 ∪ new descendants obtained from Descendant0
```

Then:

```text
Descendant2 = Descendant1 ∪ new descendants obtained from Descendant1
```

...

The process stops when:

```text
DescendantN = DescendantN-1
```

This is a **fixed point**.

### Fixed point

A fixed point is reached when an iteration produces **no new knowledge**.

---

# 15. Typical Recursive Definitions

### Supervisors

```text
supervisor(X,Y) :- reports_to(X,Y)

supervisor(X,Y) :-
    reports_to(X,Z),
    supervisor(Z,Y)
```

### Subordinates

```text
subordinate(X,Y) :- supervisor(Y,X)
```

### Components of complex aggregates

```text
component(X,Y) :- part_of(X,Y)

component(X,Y) :-
    part_of(X,Z),
    component(Z,Y)
```

---

# 16. Datalog for Constraints

Datalog can define constraints by generating examples of **incorrect database instances**.

Example:

```text
incorrectdb1(X,Y) :-
    Mother(X,Y),
    Mother(Y,X)
```

Another:

```text
incorrectdb2(X) :-
    Descendent(X,X)
```

If evaluation returns an empty result:

```text
{}
```

no incorrect instance was found.

If it returns tuples, those tuples identify potentially incorrect data.

---

# 17. Problems with Negation + Recursion

Consider:

```text
Descendant(X,Y) :- Parenthood(X,Y)

Descendant(X,Y) :-
    Descendant(X,Z),
    Parenthood(Z,Y)

NonDescendant(X,Y) :-
    Person(X,_,_),
    Person(Y,_,_),
    ¬Descendant(X,Y)
```

A naive evaluation can produce an incorrect result.

Why?

At the beginning, `Descendant` contains only direct parent-child relationships.

`NonDescendant` is therefore computed too early.

Later iterations add more descendants, but the previously computed `NonDescendant` result cannot be reduced.

Therefore, the final result can be wrong.

---

# 18. Stratification

The solution is to **stratify** the rules.

Example:

### Layer 1

```text
Descendant(X,Y) :- Parenthood(X,Y)

Descendant(X,Y) :-
    Descendant(X,Z),
    Parenthood(Z,Y)
```

First compute the complete `Descendant` relation until its fixed point.

### Layer 2

```text
NonDescendant(X,Y) :-
    Person(X,_,_),
    Person(Y,_,_),
    ¬Descendant(X,Y)
```

Only after `Descendant` is complete do we compute `NonDescendant`.

---

# 19. Stratifiable Rule Sets

A dependency graph can be built for the predicates.

Negative dependencies are labelled with:

```text
−
```

A rule set is **stratifiable** if its dependency graph has **no cycles containing negated arcs**.

In short:

```text
No cyclic dependencies through negation
```

---

# 20. Safe Rules

Two important safety requirements:

### 1. Negated variables must be grounded

Every variable in a negated literal must also occur in a positive body literal.

Not OK:

```text
S(X) :- ¬R(X)
```

OK:

```text
S(X) :- ¬R(X), P(X)
```

### 2. Negation must be stratified

There must be no cyclic dependencies involving negated literals.

Not OK:

```text
P(X) :- R(X), ¬P(X)
```

---

# 21. Another Problem with Negation

This rule is problematic:

```text
P(X) :- ¬P(X), Q(X)
```

Given:

```text
Q(1)
Q(2)
```

Evaluation alternates:

```text
Step 1: P = {(1),(2)}
Step 2: P = {}
Step 3: P = {(1),(2)}
...
```

A fixed point is never reached.

Therefore, the rule is not acceptable.

---

# 22. Functions Can Also Be Dangerous

Example:

```text
n(X+1) :- n(X)
n(1)
```

This generates:

```text
1, 2, 3, 4, ...
```

Therefore:

```text
?- n(X)
```

returns an infinite result.

Functions must therefore also be used carefully.

---

# 23. Datalog vs Relational Model Terminology

The course gives the following correspondence:

```text
Relation   → Predicate / Literal
Attribute  → Argument
Tuple      → Fact
View       → Rule
Query      → Goal
```

---

# 24. Expressive Power

Datalog with safe negation is **more expressive than Relational Algebra** because it supports:

- Recursion
- Transitive closure
- Generation of new data items

As long as rules are safe and stratified, they do not generate infinite symbols/results unintentionally.

Key comparison:

```text
Relational Algebra
    ↓
Selection
Projection
Join
Union
Difference
...
    ↓
Datalog
    +
Recursion
    +
New inferred knowledge
```

---

# 25. Why Formal Query Languages?

### Relational Algebra

Useful for understanding how **query optimization** works inside a DBMS.

### Datalog

Bridges:

```text
Databases
    ↕
Logic
    ↕
Artificial Intelligence
```

Applications mentioned in the course:

- Data Mining
- Data Analysis
- Knowledge Discovery
- Inference
- Self-correcting databases
- Domain-specific expert system

---

# Exam Essentials

- **Datalog** = logic-based query language based on rules.
    
- A **rule** has:
    
    ```text
    Head :- Body
    ```
    
- A **fact** = ground literal containing only constants.
    
- **Unification** = consistent assignment of variables using database facts.
    
- **EDB** = stored basic facts.
    
- **IDB** = inferred knowledge from rule heads.
    
- A **query** is a **goal**.
    
- Datalog without negation expresses:
    
    ```text
    σ, Π, ×, ∪
    ```
    
- Negation can express difference:
    
    ```text
    P = R − S
    ```
    
- **Safety**: variables in negated literals must also occur in positive body literals.
    
- **Recursion** allows transitive closure.
    
- Recursive evaluation continues until a **fixed point** is reached.
    
- **Stratification** prevents problematic cycles involving negation.
    
- A rule set is stratifiable if its dependency graph has **no cycles with negated arcs**.
    
- Datalog is more expressive than Relational Algebra because of:
    
    - recursion
        
    - inferred/generated data
        
- Critical distinction:
    
    ```text
    EDB = facts
    IDB = derived knowledge
    
    Relation → Predicate
    Tuple → Fact
    View → Rule
    Query → Goal
    ```