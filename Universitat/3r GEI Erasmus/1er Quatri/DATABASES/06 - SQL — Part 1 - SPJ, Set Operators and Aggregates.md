
## 1. SQL Overview

**SQL (Structured Query Language)** is a declarative language used to query and manipulate relational databases.

- **DML (Data Manipulation Language):** queries, insert/delete/update, transactions.
- **DDL (Data Definition Language):** domains, tables, indexes, authorizations, views, constraints, procedures, triggers.
- Standardization by ANSI/ISO contributed to SQL's success.
- SQL is declarative: users specify **what information they need**, not how to retrieve it.
- The DBMS query optimizer translates SQL into an internal procedural/algebraic representation.

---

## 2. Basic SQL Queries: SPJ

SPJ stands for **Select–Project–Join**, the basic structure of relational queries.

### General syntax

```
SELECT [DISTINCT] AttrExpr [AS Alias], ...
FROM Table [AS Alias], ...
WHERE Condition;
```

- `SELECT`: target list; specifies the output attributes or expressions.
- `FROM`: specifies the input tables.
- `WHERE`: optional condition used to filter tuples.
- `AS`: assigns an alias to a table or output attribute.
- `DISTINCT`: removes duplicate result rows.
- `*`: selects all attributes.

### Relational algebra interpretation

Ignoring duplicates, a query:

```
SELECT A, B
FROM R, S
WHERE Condition;
```

corresponds to:

```
πA,B (σCondition (R × S))
```

The `FROM` clause defines the Cartesian product, `WHERE` filters tuples, and `SELECT` projects the requested attributes.

**Important:** SQL normally preserves duplicates unless `DISTINCT` is specified.

### Example

```
SELECT Name
FROM Student
WHERE Dept = 'Log';
```

Returns the names of students in the Logistics department.

### Aliases

```
SELECT S.Name
FROM Student AS S;
```

Aliases improve readability and are necessary when the same table is referenced multiple times.

---

## 3. Conditions in `WHERE`

The `WHERE` clause evaluates a Boolean condition for each tuple.

### Comparison and logical operators

- Comparisons: `=`, `<>`, `<`, `>`, `<=`, `>=`
- Logical operators: `AND`, `OR`, `NOT`

```
SELECT *
FROM Student
WHERE Dept = 'CS'
  AND City = 'Bologna';
```

### Operator precedence

`AND` takes precedence over `OR`. Use parentheses when the intended grouping is different.

```
SELECT *
FROM Student
WHERE City = 'Roma'
  AND (Dept = 'CS' OR Dept = 'Log');
```

A condition such as:

```
City = 'Bologna' AND City = 'Torino'
```

is contradictory for a single tuple and produces an empty result. We should use OR.

### `BETWEEN`

```
WHERE Date BETWEEN '1990-01-01' AND '1999-12-31'
```

Checks whether a value falls within the specified range.

### `LIKE`

Pattern matching uses:

- `_` → exactly one character.
- `%` → any sequence of characters, including an empty sequence.

```
WHERE Name LIKE '_a%o'
```

Matches names whose second character is `a` and whose final character is `o`.

---

## 4. Duplicate Rows

SQL query results may contain duplicate tuples.

```
SELECT Dept
FROM Student;
```

May return `CS` multiple times.

```
SELECT DISTINCT Dept
FROM Student;
```

Returns each distinct department once.

`DISTINCT` applies to the entire selected row, not independently to each column.

---

## 5. NULL Values and Three-Valued Logic

`NULL` can represent different situations:

- A value does not apply.
- A value exists but is unknown.
- It is unknown whether the value applies.

### Checking for NULL

```
WHERE Attribute IS NULL
```

```
WHERE Attribute IS NOT NULL
```

Do not use ordinary equality to test for `NULL`.

### Three-valued logic (3VL)

SQL comparisons involving `NULL` can produce:

- `TRUE (T)`
- `FALSE (F)`
- `UNKNOWN (U)`

A tuple is selected by `WHERE` only if the condition evaluates to `TRUE`. Both `FALSE` and `UNKNOWN` are excluded.

Key rules:

```
T AND U = U
F AND U = F
T OR U  = T
F OR U  = U
NOT U   = U
U AND U = U
U OR U  = U
```

Example:

```
WHERE City = 'Milano'
```

If `City` is `NULL`, the comparison evaluates to `UNKNOWN`, so the tuple is not selected.

### Important consequence

In three-valued logic:

```
Dept = 'CS' OR Dept <> 'CS'
```

is **not** always true, because both comparisons can evaluate to `UNKNOWN` when `Dept` is `NULL`.

Adding `OR Dept IS NULL` includes those tuples.

---

## 6. Set Operators

SQL set operators combine the results of two queries.

```
SELECT ...
UNION [ALL]
SELECT ...;
```

The operators are:

- `UNION`: combines results.
- `INTERSECT`: keeps tuples present in both results.
- `EXCEPT`: keeps tuples in the first result but not the second.

![[Pasted image 20261011225946.png|519]]

Without `ALL`, duplicates are removed. With `ALL`, duplicate occurrences are preserved according to the operator's multiplicity rules.

The two queries must return compatible result structures: corresponding columns must be compatible in number and data type.

### UNION example

Find orders with an amount over 500 or a product quantity over 1000:

```
SELECT OrderId
FROM Orders
WHERE Amount > 500

UNION

SELECT OrderId
FROM Line
WHERE Qty > 1000;
```

### EXCEPT example

Find orders with an amount over 500 but no line with quantity over 1000:

```
SELECT OrderId
FROM Orders
WHERE Amount > 500

EXCEPT

SELECT OrderId
FROM Line
WHERE Qty > 1000;
```

### INTERSECT example

Find orders with an amount over 500 and at least one product quantity over 1000:

```
SELECT OrderId
FROM Orders
WHERE Amount > 500

INTERSECT

SELECT OrderId
FROM Line
WHERE Qty > 1000;
```

### `ALL` and duplicates

- `UNION ALL`: retains occurrences from both results.
- `INTERSECT ALL`: retains matching occurrences according to multiplicity.
- `EXCEPT ALL`: subtracts occurrences rather than simply removing every matching value.

### Positional notation

Set operators match columns **by position**, not by column name.

For example:

```
SELECT Father, Kid
FROM Paternity

UNION

SELECT Mother, Kid
FROM Maternity;
```

The first column of the second query is combined with the first column of the first query, even though they have different names.

---

## 7. Joins in SQL

### Join using `WHERE`

```
SELECT DISTINCT Name
FROM Student, Exam
WHERE Student.SId = Exam.SId
  AND Dept LIKE 'Log%'
  AND Grade = 30;
```

This expresses a join condition alongside other filtering conditions.

### Explicit INNER JOIN

```
SELECT Name
FROM Student
INNER JOIN Exam
  ON Student.SId = Exam.SId
WHERE Dept LIKE 'Log%'
  AND Grade = 30;
```

The explicit form separates the join condition (`ON`) from other filters (`WHERE`).

### NATURAL JOIN

```
SELECT Name
FROM Student
NATURAL JOIN Exam;
```

A `NATURAL JOIN` automatically joins on attributes with the same name.

Use it carefully: every same-named attribute participates in the natural join.

### Multiple-table joins

```
SELECT DISTINCT Name
FROM Student
JOIN Exam
  ON Student.SId = Exam.SId
JOIN Course
  ON Exam.CourseId = Course.CourseId
WHERE Title LIKE 'Mat%'
  AND Grade = 30;
```

Join conditions connect the tables, while `WHERE` filters the resulting tuples.

---

## 8. Outer Joins

Unlike an inner join, an outer join also retains unmatched tuples, filling attributes from the missing side with `NULL`.

### INNER JOIN

Returns only matching pairs.

### LEFT OUTER JOIN

Returns all rows from the left table, including those without a match on the right.

```
SELECT *
FROM Driver D
LEFT OUTER JOIN Automobile A
  ON D.DriverID = A.DriverID;
```

Drivers without cars are included; automobile columns are `NULL`.

### RIGHT OUTER JOIN

Returns all rows from the right table, including those without a match on the left.

```
SELECT *
FROM Driver D
RIGHT OUTER JOIN Automobile A
  ON D.DriverID = A.DriverID;
```

Cars without drivers are included; driver columns are `NULL`.

### FULL OUTER JOIN

Returns all matched and unmatched rows from both tables.

```
SELECT *
FROM Driver D
FULL OUTER JOIN Automobile A
  ON D.DriverID = A.DriverID;
```

Unmatched rows from either side are retained, with `NULL` values for missing attributes.

### Summary

```
INNER JOIN → matching rows only
LEFT JOIN  → all left rows + matches
RIGHT JOIN → all right rows + matches
FULL JOIN  → all rows from both sides
```

---

## 9. Self-Joins and Table Aliases

A **self-join** joins a table with itself. Different aliases distinguish its logical roles.

Example: find employees who report to Giorgio.

```
SELECT Emp.Name, Emp.Id, Boss.Id, Boss.Name
FROM Employee AS Emp
JOIN Employee AS Boss
  ON Emp.Manager = Boss.Id
WHERE Boss.Name = 'Giorgio';
```

- `Emp` represents the employee.
- `Boss` represents the manager.

Aliases are essential when the same table appears multiple times, avoiding ambiguous attribute references.

---

## 10. Sorting with `ORDER BY`

`ORDER BY` sorts the returned tuples.

### Syntax

```
SELECT ...
FROM ...
WHERE ...
ORDER BY Attribute [ASC | DESC];
```

- `ASC`: ascending order; default.
- `DESC`: descending order.
- Multiple attributes are evaluated in sequence.

Example:

```
SELECT *
FROM Orders
WHERE Amount > 1000
ORDER BY CustId ASC, Date DESC;
```

Rows are first ordered by `CustId` ascending. Within each customer, they are ordered by `Date` descending.

---

## 11. Aggregate Functions

Aggregate functions calculate global properties of a set or group of tuples.

The lecture introduces:

- `COUNT`: counts tuples or qualifying values.
- `SUM`: calculates the sum.
- `MAX`: returns the maximum.
- `MIN`: returns the minimum.
- `AVG`: calculates the average.

### One group → one tuple

Without grouping, the result set is treated as a single group.

An aggregate query typically returns one tuple containing the aggregate values.

```
SELECT MAX(Amount) AS MaxAmt
FROM Orders;
```

### SUM, MAX, MIN and AVG

```
SELECT SUM(Amount) AS SumAmt
FROM Orders
WHERE CustId = 1;
```

```
SELECT MAX(Amount) AS MaxAmt,
       MIN(Amount) AS MinAmt
FROM Orders;
```

### `ALL` and `DISTINCT`

For `SUM`, `MAX`, `MIN` and `AVG`:

```
SUM([ALL | DISTINCT] Attribute)
```

- `ALL`: considers all non-`NULL` values; default.
- `DISTINCT`: considers each distinct value once.
- `DISTINCT` is relevant to `SUM` and `AVG`; duplicates do not affect `MAX` or `MIN`.

Aggregate functions generally ignore `NULL` values for these calculations.

### COUNT

```
COUNT(*)
```

Counts tuples, regardless of their attribute values.

```
COUNT(Amount)
```

Counts tuples with a non-`NULL` `Amount`.

```
COUNT(DISTINCT CustId)
```

Counts distinct non-`NULL` customer IDs.

Examples:

```
-- Number of orders
SELECT COUNT(*)
FROM Orders;
```

```
-- Orders with a non-NULL amount
SELECT COUNT(Amount)
FROM Orders;
```

```
-- Distinct customers who placed orders
SELECT COUNT(DISTINCT CustId)
FROM Orders;
```

### Aggregates with joins

Example: find the largest order amount among orders that include product `ABC`.

```
SELECT MAX(Amount) AS MaxAmtABC
FROM Orders
NATURAL JOIN Line
WHERE ProductId = 'ABC';
```

The join identifies the relevant orders before the aggregate is applied.

---

## 12. Aggregate Functions and the Target List

The target list must be **homogeneous** when aggregates are used.

Incorrect example:

```
SELECT MAX(Amount), Date
FROM Orders
NATURAL JOIN Line
WHERE ProductId = 'ABC';
```

The problem is that `MAX(Amount)` returns a single aggregate value, but `Date` may refer to multiple orders. The query does not specify which date should be returned.

A valid example uses aggregates consistently:

```
SELECT MAX(Amount) AS MaxAm,
       MIN(Amount) AS MinAm
FROM Orders;
```

---

# Exam Essentials

## Basic query structure

```
SELECT [DISTINCT] attributes
FROM tables
WHERE condition;
```

- `SELECT` → output attributes.
- `FROM` → input relations.
- `WHERE` → tuple filtering.
- `DISTINCT` → removes duplicate result rows.

## Relational algebra correspondence

```
SELECT A FROM R WHERE P
≈ πA(σP(R))
```

For multiple tables, the basic interpretation uses a Cartesian product followed by selection and projection, ignoring SQL duplicates.

## Set operators

```
UNION     → union
INTERSECT → intersection
EXCEPT    → difference
```

- Without `ALL`: duplicates are removed.
- With `ALL`: duplicate occurrences are preserved according to the operator.

## NULL

- Test with `IS NULL` / `IS NOT NULL`.
- Comparisons involving `NULL` can produce `UNKNOWN`.
- `WHERE` retains only tuples whose condition is `TRUE`.

## Joins

```
INNER → matched rows
LEFT  → all left rows
RIGHT → all right rows
FULL  → all rows from both sides
```

- `ON` specifies the join condition.
- `NATURAL JOIN` automatically uses same-named attributes.
- A self-join requires aliases.

## Sorting

```
ORDER BY A ASC, B DESC;
```

Sorting is sequential: `B` breaks ties in `A`.

## Aggregates

```
COUNT(*)             -- number of tuples
COUNT(A)             -- number of non-NULL A values
COUNT(DISTINCT A)    -- number of distinct non-NULL A values
SUM(A)
AVG(A)
MAX(A)
MIN(A)
```

**Remember:** without grouping, the aggregate query treats the result set as one group and produces one tuple. Do not mix aggregate results with ordinary attributes unless the query defines how those attributes are selected.