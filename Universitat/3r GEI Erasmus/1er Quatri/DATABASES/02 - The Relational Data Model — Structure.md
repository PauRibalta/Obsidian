

## 1. Data Models

A **data model** provides a simplified and structured description of the real world.
It captures the aspects needed to understand and represent a domain.

### Main data models

- Hierarchical
- Network
- Relational
- Object
- XML

The relational model is a **logical data model**.

---

## 2. Conceptual vs Logical Models

### Conceptual Data Model

Describes the real world independently of implementation.

Example:

**Entity-Relationship Model**

- objects have identity, name and attributes
- associations have properties such as cardinalities and attributes

### Logical Data Model

Defines how data are logically represented.

Examples:

- Hierarchical: data are represented as records and associations among data are depicted by pointers in a tree structure
- Network: data are represented as records and associations among data are depicted by pointers in a graph structure
- Relational: data are represented as tables and associations among data are built-up by comparing the values of specific attributes in different tables

---
## 3. History of the Relational Model

The relational model was invented by **Edgar "Ted" Codd in 1970**.

Early systems:

- SYSTEM R
- Ingres

First commercial systems appeared in the early 1980s.

The relational model became commercially successful from around 1985 onwards.

### Key advantages

- **Logical independence**
- **Physical independence**
- Easy support for **declarative queries**, especially SQL

---

## 4. Three-Level Logical Architecture 

A database can be viewed through three levels:

### External Schema

- description for specific applications
- different users/applications may have different views
### Logical Schema

- global description of the database

### Internal Schema

- internal mechanisms used to manage the data

```text
External Schema
       ↓
Logical Schema
       ↓
Internal Schema
       ↓
Physical Data
```

### Logical independence

Different users can perceive different views of the data.

### Physical independence

Users can ignore the physical details of how data are managed.

![[Pasted image 20260918172936.png|589]]

---

## 5. Relational Model: Informal Definition

A relation is informally represented as a **table**.

```text
Student
--------------------------------
SId | Name    | City    | Dept
--------------------------------
123 | Carlo   | Bologna | CS
415 | Paola   | Torino  | CS
702 | Antonio | Roma    | Log
```

Terminology:

- Table → relation
- Column → attribute
- Row → tuple

---

## 6. Formal Definition 

### Domain

A **domain** DD is an arbitrary set of values.
### Cartesian Product

Given domains, their Cartesian product contains all possible nn-tuples:
### Relation

A relation RR is any subset of the Cartesian product:

From the empty relation all the way to the universe relation.

---

## 7. Positional vs Non-Positional Relations

### Positional structure

The position of each value matters.

The i-th value in the n-uple comes from the i-th domain.
### Non-positional structure

The domains have **names (attributes)**.

Therefore, their order is irrelevant.

The relational model uses **non-positional relations**.

---

## 8. Degree and Cardinality 

### Degree

Number of attributes/domains in a relation.
Degree=nºcolumns
### Cardinality

Number of tuples/rows in a relation.
Cardinality=nºrows

Attribute names within one relation must be unique.

---

## 9. Relation Schema

A relation schema is written as:

```text
RelationName(attr1, ..., attrn)
```

Example:

```text
Student(SId, Name, City, Dept)
```

Relation names must be unique.

---

## 10. Formal vs Informal Terminology

|Formal|Informal|
|---|---|
|Relation|Table|
|Attribute|Column|
|Tuple / n-tuple|Row|
|Domain|Data type|
|Cardinality|Number of rows|
|Degree|Number of columns|

### Important difference

Formal relational model:

- **no duplicate tuples**

Informal/table representation:

- duplicates may exist

---

## 11. Linking Relations

In the relational model:

- data items are stored in relations
- associations between different relations are created by comparing attribute values

Associations are intrinsically **bi-directional**.

Example:

```text
STUDENT(SId, ...)
EXAM(SId, CourseId, Date, Grade)
```

The common `SId` links students with their exams.

---

## 12. Schema vs Instance

### Schema

Defines the structure:

```text
Student(SId, Name, City, Dept)
```

### Instance

Contains the actual tuples currently stored.

Schema and instance have different roles:

- schema design/maintenance
- instance management
And a different frequency of change

---

## 13. Integrity Constraints

Integrity constraints prevent database instances that do not correctly represent the application domain.

Main types:

- keys
- null values
- referential integrity
- generic constraints

---

# 14. Keys 

A **key** is a subset of the attributes of a relation that is:

1. **Unique**: No two tuples can have the same values for the key attributes.
2. **Minimal**: Removing any attribute from the key destroys uniqueness.

---

## 15. Superkey vs Key 

For:

R(A1,A2,…,An)

A set of attributes KK is a **superkey** if no two distinct tuples have the same values on KK.

A **key** is a superkey that is also **minimal**.

**Key=Minimal Superkey**

---

## 16. Choosing Keys

A key must be chosen according to the **general properties of the application data**, not just the current instance.

A combination that is unique today may not remain unique in the future.

Therefore:

> Key choice must consider the expected properties of the data.

---

## 17. Does Every Relation Have a Key? 

#### Yes.

A relation is a set, and sets cannot contain duplicate elements.
Therefore, the set containing **all attributes** is always a superkey.
By removing unnecessary attributes, at least one minimal superkey (key) exists.

Every relation has at least one key

---

## 18. Primary and Secondary Keys

A relation can have multiple keys.

	- One selected key → **Primary key**
- Other keys → **Secondary keys**

Example:

```text
Customer(CustId, Address, SSN, email, ...)
```

Possible:

- Primary key → `CustId`
- Secondary keys → `SSN`, `email`

---

## 19. Importance of Keys

Keys provide:

- unique access to individual tuples/attribute values
- a mechanism for linking data in different relations

A value can be identified through:

1. relation name
2. key value
3. attribute name

---

## 20. Keys and NULL 

`NULL` values are **not allowed in attributes forming the chosen primary key**.

Reason:
- NULL does not provide the necessary identification
- NULL does not support references between relations

---

## 21. Queries and Semantics

Queries are questions posed to data based on their **meaning/semantics**.

Data models systematically describe:
- elements
- properties
- structure
of a dataset in a standard way

Datasets represented within the same data model are easier to compare, combine, integrate.

---

## 22. Relational Model — Dataset Properties

A relational DBMS is designed to enforce constraints.
### Necessarily

- identification → primary keys
- simple and homogeneous data types
- multiple values flattened into atomic values
### Optionally

- referential integrity
- domain restrictions
- global constraints/assertions
- triggers/reactive behaviours

**Rule of thumb:**
- More constraints generally mean more curated and trustworthy datasets.

---

## 23. Relational Model vs XML

XML is an example of a hierarchical model.

XML documents:

- must have a tree structure
- are ordered
- have textual representation
- may mix schema and instance
- intermix markup and text
- have no `NULL` values
- missing data is simply absent

---

# Exam Essentials

- **Data model** → simplified/structured description of the real world
    
- Relational model → data represented as **tables**
    
- Relational model → associations built by comparing attribute values
    
- **Schema** → structure
    
- **Instance** → actual data at a point in time
    
- **Relation** → subset of a Cartesian product
    
- **Domain** → set of possible values
    
- **Tuple** → ordered nn-tuple
    
- **Attribute** → named domain occurrence
    
- **Degree** → number of attributes/columns
    
- **Cardinality** → number of tuples/rows
    
- Formal relations → **no duplicate tuples**
    
- **Superkey** → guarantees uniqueness
    
- **Key** → minimal superkey
    
- **Primary key** → chosen key
    
- Other keys → **secondary keys**
    
- Every relation has **at least one key**
    
- Key must be unique **and minimal**
    
- Key selection must consider the **general properties of the application**, not just the current instance
    
- Primary key attributes → **no NULL**
    
- Relations link through **attribute values**
    
- Three-level architecture:
    
    - external schema
        
    - logical schema
        
    - internal schema
        
- **Logical independence** → different views of data
    
- **Physical independence** → ignore physical storage details
    
- Integrity constraints → prevent illegal/incorrect instances