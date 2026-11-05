## 1. Grouping

Grouping divides the tuples of a query result into **homogeneous subsets (groups)**. Aggregate functions are then computed separately for each group.

- `GROUP BY`: Defines how groups are constructed.
- `HAVING`: Filters groups according to a condition.
- Each group produces one result tuple: **one group → one tuple**.

### Query structure

```
SELECT ...
FROM ...
WHERE ...
GROUP BY ...
HAVING ...
ORDER BY ...;
```

### Example: Aggregate orders by customer

Find the total amount of orders placed after 10-6-17 for each customer.

```
SELECT CustId, SUM(Amount)
FROM Order
WHERE Date > '2017-06-10'
GROUP BY CustId;
```

The `WHERE` clause filters individual orders before grouping. `SUM(Amount)` is then computed separately for each customer.

## 2. Grouping with HAVING

`HAVING` filters groups, often using aggregate functions.

Example: Find the total amount of orders placed after 10-6-17 for customers who placed at least two orders during that period.

```
SELECT CustId, SUM(Amount)
FROM Order
WHERE Date > '2017-06-10'
GROUP BY CustId
HAVING COUNT(*) >= 2;
```

### Logical processing steps

1. **WHERE:** Filter individual tuples.
2. **GROUP BY:** Partition the remaining tuples into groups.
3. **Aggregates:** Compute aggregate functions for every group.
4. **HAVING:** Remove groups that do not satisfy the condition.
5. **SELECT:** Produce the output attributes and aggregate values.
6. **ORDER BY**, if present: Sort the resulting tuples.

### WHERE vs. HAVING

- `WHERE` filters individual tuples **before grouping**. It cannot be used to filter groups based on their aggregate values.
- `HAVING` filters groups **after grouping and aggregate computation**.

Example: Find departments whose average salary among employees in office 20 is greater than 25,000.

```
SELECT Department
FROM Employee
WHERE Office = '20'
GROUP BY Department
HAVING AVG(Salary) > 25000;
```

Here, `WHERE` restricts the employees included in the calculation, while `HAVING` filters departments according to their average salary.

## 3. SELECT and GROUP BY

When grouping is used, non-aggregated expressions in the `SELECT` list must be compatible with the grouping.

Incorrect:

```
SELECT Date
FROM Order
GROUP BY CustId;
```

`Date` can have multiple values within the same customer group.

Incorrect:

```
SELECT CustId, COUNT(*), Amount
FROM Order NATURAL JOIN Customer
GROUP BY CustId;
```

`Amount` is not uniquely determined by `CustId` because a customer can place multiple orders.

Correct:

```
SELECT CustId, COUNT(*), Address
FROM Order NATURAL JOIN Customer
GROUP BY CustId;
```

In this example, `CustId` determines the customer's `Address`, so there is one address for each group.

**Key rule:** A non-aggregated expression in the target list must have a unique value for each group, typically because it is a grouping attribute or functionally determined by the grouping attributes.

## 4. Grouping and Sorting

`ORDER BY` sorts the final query result according to the specified expressions.

Example: Find each customer's total order amount after 10-6-17, retaining only customers with at least two orders, and sort by decreasing total amount.

```
SELECT CustId, SUM(Amount)
FROM Order
WHERE Date > '2017-06-10'
GROUP BY CustId
HAVING COUNT(*) >= 2
ORDER BY SUM(Amount) DESC;
```

- `ASC`: Ascending order (default).
- `DESC`: Descending order.

The result is grouped and filtered first, then sorted.

## 5. Grouping by Multiple Attributes

`GROUP BY` can contain multiple attributes. Each distinct combination defines a separate group.

Example: Find the total quantity of every product ordered by each customer, keeping only combinations whose total quantity exceeds 50.

```
SELECT CustId, ProductId, SUM(Qty)
FROM Order NATURAL JOIN Line
GROUP BY CustId, ProductId
HAVING SUM(Qty) > 50;
```

The grouping key is `(CustId, ProductId)`. Quantities are summed independently for each customer-product pair.

## 6. Nested Queries

A nested query (subquery) is a query embedded inside another SQL query. Its result can be used in predicates, such as those in `WHERE`.

### ANY and ALL

General syntax:

```
ScalarValue Operator ANY (SELECT ...)
ScalarValue Operator ALL (SELECT ...)
```

Available comparison operators include `=`, `<>`, `<`, `<=`, `>`, and `>=`.

- `ANY`: The predicate is true if the comparison holds for **at least one** value returned by the subquery.
- `ALL`: The predicate is true if the comparison holds for **every** value returned by the subquery.

For example:

```
SELECT OrderId
FROM Order
WHERE Amount > ANY (
    SELECT Amount
    FROM Order
);
```

This selects orders whose amount is greater than at least one order amount.

```
SELECT OrderId
FROM Order
WHERE Amount >= ALL (
    SELECT Amount
    FROM Order
);
```

This selects orders whose amount is greater than or equal to every order amount, i.e. orders with the maximum amount.

### Scalar subqueries

A scalar subquery is expected to return a single value: one column and one row.

Example: Find the earliest order.

```
SELECT *
FROM Order
WHERE Date = (
    SELECT MIN(Date)
    FROM Order
);
```

The inner query returns the minimum date, which is compared with each order's date.

## 7. Nested Queries and Equivalent Joins

Subqueries can often be replaced by joins, and vice versa.

### Example 1: Orders containing products priced above €100

Using a nested query:

```
SELECT OrderId
FROM Line
WHERE ProductId = ANY (
    SELECT ProductId
    FROM Product
    WHERE Price > 100
);
```

Equivalent join formulation:

```
SELECT OrderId
FROM Line NATURAL JOIN Product
WHERE Price > 100;
```

Both express the relationship between order lines and products whose prices exceed 100.

### Example 2: Products ordered together with product 'ABC'

Using a self-join:

```
SELECT L1.ProductId
FROM Line L1
JOIN Line L2 ON L1.OrderId = L2.OrderId
WHERE L2.ProductId = 'ABC';
```

Equivalent nested query:

```
SELECT ProductId
FROM Line
WHERE OrderId = ANY (
    SELECT OrderId
    FROM Line
    WHERE ProductId = 'ABC'
);
```

The inner query finds orders containing `ABC`; the outer query finds products in those orders.

## 8. Negation with Nested Queries

To find orders that do not contain product `ABC`:

```
SELECT OrderId
FROM Order
WHERE OrderId <> ALL (
    SELECT OrderId
    FROM Line
    WHERE ProductId = 'ABC'
);
```

Equivalent formulation using `EXCEPT`:

```
SELECT OrderId
FROM Order
EXCEPT
SELECT OrderId
FROM Line
WHERE ProductId = 'ABC';
```

### IN and NOT IN

- `IN` is equivalent to `= ANY`.
- `NOT IN` is equivalent to `<> ALL`.

Example:

```
SELECT DISTINCT OrderId
FROM Order
WHERE OrderId NOT IN (
    SELECT OrderId
    FROM Line
    WHERE ProductId = 'ABC'
);
```

**Important:** SQL's handling of `NULL` can affect the behaviour of `NOT IN` and comparisons with `ALL`. Do not assume that negated predicates behave like ordinary two-valued Boolean logic when the subquery can return `NULL`.

## 9. Duplicates: Nested Queries vs. Joins

Example: Find customers who placed at least one order with an amount above 10,000.

```
SELECT CustId, FullName, Address
FROM Customer
WHERE CustId IN (
    SELECT CustId
    FROM Order
    WHERE Amount > 10000
);
```

Equivalent join formulation:

```
SELECT DISTINCT CustId, FullName, Address
FROM Customer NATURAL JOIN Order
WHERE Amount > 10000;
```

A customer can have multiple qualifying orders, so a join may produce duplicate customer tuples. `DISTINCT` removes them.

The nested query expresses membership in the set of qualifying customer IDs without multiplying customer tuples due to multiple matching orders.

## 10. Multiple Levels of Nesting

Nested queries can be embedded inside other nested queries.

Example: Find customers who placed at least one order containing a product named `Tyre`.

```
SELECT CustId, FullName, Address
FROM Customer
WHERE CustId IN (
    SELECT CustId
    FROM Order
    WHERE OrderId IN (
        SELECT OrderId
        FROM Line
        WHERE ProductId IN (
            SELECT ProductId
            FROM Product
            WHERE Name = 'Tyre'
        )
    )
);
```

The subqueries progressively identify:

1. Product IDs for products named `Tyre`.
2. Order IDs containing those products.
3. Customers who placed those orders.
4. The corresponding customer information.

An equivalent solution joins `Customer`, `Order`, `Line`, and `Product`, then filters by `Name = 'Tyre'`. Because multiple matching orders or lines may produce duplicates, the join formulation may require `DISTINCT`.

## 11. MIN and MAX Using Nested Queries

Aggregate functions such as `MIN` and `MAX` can be expressed using nested queries and comparisons with `ALL`.

### Largest order

Using `MAX`:

```
SELECT OrderId
FROM Order
WHERE Amount = (
    SELECT MAX(Amount)
    FROM Order
);
```

Using `ALL`:

```
SELECT OrderId
FROM Order
WHERE Amount >= ALL (
    SELECT Amount
    FROM Order
);
```

Both formulations identify orders whose amount is maximal.

### Why aggregates cannot be used directly in WHERE

Incorrect:

```
SELECT OrderId
FROM Order
WHERE Amount = MAX(Amount);
```

This is invalid because `WHERE` is evaluated tuple by tuple before groups and their aggregates are computed. The aggregate must instead be computed in a suitable subquery or through grouping.

## 12. Tuple Constructors

A **tuple constructor** combines multiple expressions into a tuple using parentheses. This allows a comparison to involve multiple attributes together.

Example: Find the order(s) with the largest amount for each customer.

```
SELECT *
FROM Order
WHERE (CustId, Amount) IN (
    SELECT CustId, MAX(Amount)
    FROM Order
    GROUP BY CustId
);
```

The inner query returns one pair `(CustId, MAX(Amount))` for each customer. The outer query selects orders matching one of these pairs.

Tuple constructors are useful when the comparison must be checked for multiple attributes together rather than independently.

## 13. The EXISTS Operator

Syntax:

```
[NOT] EXISTS (SELECT ...)
```

- `EXISTS` is true if the subquery returns at least one tuple.
- `NOT EXISTS` is true if the subquery returns no tuples.
- The selected values are irrelevant to the existence test; `SELECT *` is commonly used.

### Correlated subqueries (bindings)

A nested query can use variables from the outer query. This is called a **correlated subquery** or a query with bindings.

The inner query is evaluated in relation to each candidate tuple from the outer query.

Example: Find customers who placed more than one order on the same day.

```
SELECT CustId
FROM Order O
WHERE EXISTS (
    SELECT *
    FROM Order O1
    WHERE O1.CustId = O.CustId
      AND O1.Date = O.Date
      AND O1.OrderId <> O.OrderId
);
```

The inner query searches for another order by the same customer on the same date. `O1.OrderId <> O.OrderId` ensures that it is a different order.

### Example: Students with or without homonyms

A homonym is another student with the same name and surname.

Find students who have homonyms:

```
SELECT *
FROM Student S
WHERE EXISTS (
    SELECT *
    FROM Student S1
    WHERE S1.Name = S.Name
      AND S1.Surname = S.Surname
      AND S1.SSN <> S.SSN
);
```

To find students who do **not** have homonyms, replace `EXISTS` with `NOT EXISTS`.

The condition `S1.SSN <> S.SSN` ensures that the matching student is not the same student.

## 14. Alternative Formulations for Correlated Queries

The same relationship can often be expressed using `EXISTS`, `IN`, `ANY`, `ALL`, or joins.

For the homonym example, equivalent approaches can use:

- `EXISTS` / `NOT EXISTS`
- `IN` / `NOT IN`
- Tuple constructors such as `(Name, Surname)`
- Comparisons using `ANY` or `ALL`

The key is to preserve both:

1. The required relationship between the attributes.
2. The condition ensuring that the two tuples represent different students.

### Visibility and scope of variables

Variables (table aliases) have hierarchical scope, as in structured programming languages.

An alias defined inside one subquery is not automatically visible in a separate subquery.

For example, an alias `O1` defined inside one subquery cannot be referenced by a sibling subquery outside its scope. An outer alias can be referenced by a nested query when it is within scope.

## 15. Families of Equivalent Operators

The slides identify several families of operators with roughly equivalent expressive power, ignoring duplicates.

|Relationship|Alternative formulations|
|---|---|
|Positive relationship based on equality|Equi-join, `IN`, `= ANY`, `EXISTS`|
|Negation of an equality-based relationship|`EXCEPT`, `NOT IN`, `<> ALL`, `NOT EXISTS`|
|Existential relationship using a comparison other than equality|Theta-join, `<comparison> ANY`|
|Universal comparison using an order relationship|`MIN`/`MAX` with grouping, `<comparison> ALL` (with the restrictions indicated in the slides)|

Interpretation:

- Positive relationships express the existence of a matching tuple.
- Negative relationships express the absence of a tuple satisfying the corresponding positive condition.
- `ANY` expresses that at least one tuple satisfies the comparison.
- `ALL` expresses that every tuple satisfies the comparison.

These formulations are not always interchangeable without considering duplicates, `NULL`, and the exact comparison semantics.

## 16. Example: Employees Earning More Than Their Managers

Schema:

```
Employee(Id, Name, Salary, Manager)
```

### Using a self-join

```
SELECT E.Id
FROM Employee E
JOIN Employee B ON E.Manager = B.Id
WHERE E.Salary > B.Salary;
```

`E` represents the employee and `B` the manager.

### Using ANY

```
SELECT E.Id
FROM Employee E
WHERE E.Salary > ANY (
    SELECT B.Salary
    FROM Employee B
    WHERE E.Manager = B.Id
);
```

### Using EXISTS

```
SELECT E.Id
FROM Employee E
WHERE EXISTS (
    SELECT *
    FROM Employee B
    WHERE E.Manager = B.Id
      AND E.Salary > B.Salary
);
```

The binding `E.Manager = B.Id` identifies the manager, and `E.Salary > B.Salary` checks the salary condition.

## 17. Nesting in SELECT and FROM

Nested queries are also possible in the `SELECT` and `FROM` clauses.

### Nesting in SELECT

A subquery used as a single target-list value must return a **scalar value**: one column and one row. Its result must be compatible with the field in which it is used.

### Nesting in FROM

A nested query in `FROM` defines a derived table. It must have an alias so that its resulting tuples can be referenced.

Conceptually, this is similar to defining a temporary, unnamed view for that query: it is not stored as part of the database schema.

## Exam Essentials

- `GROUP BY` creates groups; aggregate functions are evaluated separately for each group.
    
- `WHERE` filters tuples before grouping; `HAVING` filters groups after aggregate computation.
    
- Non-aggregated expressions in `SELECT` must be compatible with the grouping and uniquely determined for each group.
    
- `ORDER BY ... DESC` sorts results in decreasing order.
    
- Multiple grouping attributes define groups by their distinct combinations.
    
- `ANY` means at least one comparison is true; `ALL` means every comparison is true.
    
- `IN` is equivalent to `= ANY`; `NOT IN` is equivalent to `<> ALL`.
    
- Use a scalar subquery when a single value is needed, such as `MAX(Amount)`.
    
- Do not put an aggregate such as `MAX()` directly in `WHERE`; calculate it in a subquery or grouped query.
    
- Tuple constructors compare multiple attributes together, e.g. `(CustId, Amount)`.
    
- `EXISTS` checks whether a subquery returns at least one tuple; `NOT EXISTS` checks that it returns none.
    
- Correlated subqueries can refer to outer-query aliases, but variable visibility follows scope rules.
    
- Joins can produce duplicates; `DISTINCT` may be required when replacing a nested query with a join.
    
- Be careful with `NULL` when using negated predicates such as `NOT IN`.
    
- Recognize equivalent formulations using joins, `IN`, `ANY`, `ALL`, `EXISTS`, and `EXCEPT`.