## 1. Data Definition Language (DDL)

DDL defines the structure of a database: schemas, tables, domains, constraints, indexes and other objects.

### 1.1 Domains

A **domain** specifies the valid values for an attribute.

Two types:

- **Built-in domains:** predefined SQL data types.
- **User-defined domains:** restrictions of built-in domains that can be reused.

#### Built-in data types

|Category|Types|Purpose|
|---|---|---|
|Character strings|`CHAR(n)`, `VARCHAR(n)`|Fixed-length and variable-length strings|
|Exact numeric|`NUMERIC(p,s)`, `DECIMAL(p,s)`, `INTEGER`, `SMALLINT`|Integers and exact decimal values|
|Approximate numeric|`FLOAT`, `REAL`, `DOUBLE PRECISION`|Floating-point values|
|Date and time|`DATE`, `TIME`, `TIMESTAMP`|Temporal values|
|Intervals|`INTERVAL`|Time durations|
|Bits|`BIT(n)`, `BIT VARYING(n)`|Single bits or bit sequences|

- `NUMERIC(p,s)`: `p` is the precision (total digits), and `s` is the scale (digits after the decimal point).
- Example: `NUMERIC(5,2)` allows values from `-999.99` to `+999.99`.
- `TIME` and `TIMESTAMP` may support time zones.
- SQL:1999 introduces `BLOB` (Binary Large Object) and `CLOB` (Character Large Object).

#### User-defined domains

Syntax:

```
CREATE DOMAIN DomainName AS ElementaryDomain
    [DEFAULT value]
    [CONSTRAINTS];
```

Example:

```
CREATE DOMAIN Grade AS SMALLINT DEFAULT 18;
```

Domains improve readability and allow the same definition to be reused in multiple tables.

### 1.2 Default and NULL

**DEFAULT** specifies the value used when an attribute is omitted during insertion.

```
CREATE TABLE Employee (
    Id INTEGER,
    Salary NUMERIC(8,2) DEFAULT 0
);
```

The default can be a compatible value, `USER` or `NULL`.

**NULL** represents an unknown or inapplicable value. It can mean:

- A value exists but is unknown.
- A value does not apply.
- It is unknown whether the value applies.

`NULL` is not equivalent to zero, an empty string or a normal value.

### 1.3 Schema definition

A schema is a collection of database objects, such as domains, tables, indexes, views and privileges. It has a name and an owner.

```
CREATE SCHEMA SchemaName AUTHORIZATION UserName;
```

### 1.4 Table definition

`CREATE TABLE` defines a table's attributes and constraints and creates an empty table.

```
CREATE TABLE Student (
    SId CHAR(6) PRIMARY KEY,
    Name VARCHAR(30) NOT NULL,
    City VARCHAR(20),
    Dept CHAR(3)
);

CREATE TABLE Course (
    CourseId CHAR(6) PRIMARY KEY,
    Name VARCHAR(30) NOT NULL,
    Professor VARCHAR(20)
);

CREATE TABLE Exam (
    SId CHAR(6),
    CourseId CHAR(6),
    ExamDate DATE NOT NULL,
    Grade SMALLINT NOT NULL,
    PRIMARY KEY (SId, CourseId)
);
```

### 1.5 Constraints

Constraints specify which database instances are valid.

They can be:

- **Intra-relational:** within one table.
    - Tuple-level: applies to an individual attribute/tuple.
    - Table-level: applies to the table or combinations of attributes.
- **Inter-relational:** between tables, mainly through referential integrity.

#### `NOT NULL`

An attribute cannot contain `NULL`.

```
Name VARCHAR(30) NOT NULL
```

#### `UNIQUE`

Prevents duplicate values and defines candidate keys.

```
Email VARCHAR(100) UNIQUE
```

For a combination of attributes:

```
UNIQUE (Name, Surname)
```

This means that the **combination** must be unique. It does not require each attribute to be individually unique.

By contrast:

```
Name VARCHAR(20) UNIQUE,
Surname VARCHAR(20) UNIQUE
```

is more restrictive because both attributes must be individually unique.

#### `PRIMARY KEY`

Defines the table's primary key. Each table has exactly one primary key constraint, which may contain multiple attributes.

```
PRIMARY KEY (SId, CourseId)
```

A primary key implies both `UNIQUE` and `NOT NULL`.

#### `CHECK`

Specifies a condition that must always be true.

```
Age INTEGER CHECK (Age > 0)
```

### 1.6 Referential integrity: foreign keys

A **foreign key** ensures that its values refer to existing values in the referenced table.

- **External/referenced table:** contains the referenced key.
- **Internal/referencing table:** contains the foreign key.

Example:

```
CREATE TABLE Exam (
    SId CHAR(6),
    CourseId CHAR(6),
    ExamDate DATE NOT NULL,
    Grade SMALLINT NOT NULL,
    PRIMARY KEY (SId, CourseId),
    FOREIGN KEY (SId) REFERENCES Student(SId),
    FOREIGN KEY (CourseId) REFERENCES Course(CourseId)
);
```

The foreign key values must exist in the referenced columns, except where `NULL` is permitted.

#### Referential integrity reaction policies

Policies specify what happens to referencing rows when a referenced value is deleted or updated.

```
FOREIGN KEY (SId) REFERENCES Student(SId)
    ON DELETE CASCADE
    ON UPDATE CASCADE
```

|Policy|Effect|
|---|---|
|`CASCADE`|Propagates the deletion or update to referencing rows|
|`SET NULL`|Sets the foreign key to `NULL`|
|`SET DEFAULT`|Sets the foreign key to its default value|
|`NO ACTION`|Blocks the operation if it would violate referential integrity|

Important:

- `SET NULL` requires the foreign key to permit `NULL`.
- `SET DEFAULT` requires a suitable default value.
- These actions may be inapplicable when the foreign key is part of the primary key.
- `NO ACTION` prevents an operation that would leave invalid references.

#### Example: deleting a student

Suppose `Exam.SId` references `Student.SId`.

- `ON DELETE CASCADE`: the student's exams are also deleted.
- `ON DELETE SET NULL`: the exams remain, but their `SId` becomes `NULL`.
- `ON DELETE SET DEFAULT`: the exams receive the default `SId`.
- `ON DELETE NO ACTION`: deletion of the student is rejected if exams still reference them.

### 1.7 Indexes

An **index** is a data structure that facilitates efficient access to data according to a search criterion.

```
CREATE INDEX DataIx ON Orders(OrderDate);

CREATE UNIQUE INDEX OrdKey ON Orders(OrderId);
```

A unique index prevents duplicate indexed values.

### 1.8 Modifying and removing schema objects

#### `ALTER`

Modifies an existing domain or table.

```
ALTER TABLE Orders
    ADD COLUMN InvoiceNumber CHAR(6);

ALTER TABLE Orders
    ALTER COLUMN Amount SET DEFAULT 0;

ALTER TABLE Orders
    DROP COLUMN OrderDate;
```

#### `DROP`

Removes schema objects, such as tables, domains, indexes and views.

```
DROP TABLE Orders;
DROP INDEX DataIx;
```

Two options:

- `RESTRICT`: refuses the removal when dependent objects prevent it.
- `CASCADE`: also removes dependent objects or relationships.

**Exam tip:** `DELETE` removes rows; `DROP TABLE` removes the table itself, including its schema.

### 1.9 Relational catalogue

The **relational catalogue** (or data dictionary) stores descriptions of the database's structures and metadata.

According to the slides, SQL-2 distinguishes:

- `Definition_Schema`: tables describing database structures.
- `Information_Schema`: views defined over the definition schema.

The information schema describes objects such as tables, columns, domains, constraints, authorizations and other database metadata.

The catalogue is maintained automatically by the DBMS and should not be modified directly by users.

---

## 2. Data Manipulation Language (DML)

DML changes the **instance** of a database: the rows and their attribute values.

The main commands are:

- `INSERT`: adds rows.
- `DELETE`: removes rows.
- `UPDATE`: modifies attribute values.

These commands are **set-oriented**: a single command can affect multiple rows.

### 2.1 INSERT

Syntax:

```
INSERT INTO TableName [(AttributeList)]
VALUES (ValueList);
```

Example:

```
INSERT INTO Student (SId, Name, City, Dept)
VALUES ('456878', 'Giorgio Rossi', 'Bologna', 'Logistics');
```

A query can also supply the rows:

```
INSERT INTO Bolognesi (Id, Name, City, Dept)
SELECT SId, Name, City, Dept
FROM Student
WHERE City = 'Bologna';
```

Rules:

- Attribute order and value order must correspond.
- If the attribute list is omitted, values must follow the table's attribute order.
- If some attributes are omitted, they receive their default values or `NULL` if no default is defined.
- The inserted rows must satisfy the table's constraints.

### 2.2 DELETE

Syntax:

```
DELETE FROM TableName
WHERE Condition;
```

Delete a specific student:

```
DELETE FROM Student
WHERE SId = '678678';
```

Delete students who have never passed an exam, using the example's condition:

```
DELETE FROM Student
WHERE SId NOT IN (
    SELECT SId
    FROM Exam
);
```

Important:

- `DELETE` removes every row satisfying the condition.
- **Without** `**WHERE**`**, all rows are removed**, but the table remains.
- Foreign-key policies may also cause changes in other tables.

```
DELETE FROM Student;
```

This empties `Student` without removing its schema.

```
DROP TABLE Student CASCADE;
```

This removes the table itself and may also remove dependent objects.

### 2.3 UPDATE

Syntax:

```
UPDATE TableName
SET Attribute = Expression
WHERE Condition;
```

Multiple attributes can be assigned in one command.

Set exam grades to 30:

```
UPDATE Exam
SET Grade = 30
WHERE ExamDate = '2013-04-01';
```

Increase selected grades by one:

```
UPDATE Exam
SET Grade = Grade + 1
WHERE SId = '787989';
```

An assignment can use an expression, a subquery, `NULL` or `DEFAULT`.

#### Updating using a nested query

Increase the amount of every order containing product `456` by 5:

```
UPDATE Orders
SET Amount = Amount + 5
WHERE OrderId IN (
    SELECT OrderId
    FROM Line
    WHERE ProductId = '456'
);
```

Calculate the total number of pieces in each order:

```
UPDATE Orders O
SET NumPieces = (
    SELECT SUM(Qty)
    FROM Line L
    WHERE L.OrderId = O.OrderId
);
```

The nested query refers to the current order through `O.OrderId`.

#### Order of UPDATE commands

The order in which commands execute can affect the result because each command operates on the current database state.

For example:

```
UPDATE Employee
SET Salary = Salary * 1.1
WHERE Salary <= 30;

UPDATE Employee
SET Salary = Salary * 1.15
WHERE Salary > 30;
```

An employee initially earning 30 or less may receive both increases if the first update raises their salary above 30. The second condition is evaluated after the first command.

**Exam tip:** Check whether an earlier update changes the condition used by a later update.

---

## 3. Access Control and Privileges

### 3.1 Privacy and authorization

Database privacy protects sensitive information against unauthorized access, disclosure and modification.

- **Authentication:** identifies the user, for example through a username and password.
- **Authorization:** determines which resources and operations a user is permitted to access.

The owner of a resource can assign privileges to other users. A system administrator/root user has broad administrative access.

A privilege is characterized by the resource, permitted action, granting user, receiving user and whether the receiver can pass the privilege to others.

### 3.2 Types of privileges

|Privilege|Meaning|
|---|---|
|`SELECT`|Read/query a resource|
|`INSERT`|Insert rows|
|`UPDATE`|Modify data|
|`DELETE`|Remove rows|
|`REFERENCES`|Use a resource in referential-integrity constraints|
|`USAGE`|Use a resource in schema definitions, such as a domain|
|`ALL PRIVILEGES`|Shortcut for all applicable privilege types|

Specific DBMSs may offer additional privileges.

### 3.3 GRANT

Assigns privileges to users.

```
GRANT SELECT, INSERT ON Student TO UserA;
```

To allow the receiver to grant the privileges to other users:

```
GRANT SELECT ON Student TO UserA
WITH GRANT OPTION;
```

`WITH GRANT OPTION` allows the receiving user to propagate the privilege.

### 3.4 REVOKE

Removes privileges.

```
REVOKE SELECT ON Student FROM UserA;
```

Options:

- `RESTRICT`: revokes the privilege only from the specified users; the operation may be refused if dependent grants exist.
- `CASCADE`: also revokes privileges that those users granted onward.

---

## 4. Views

### 4.1 Definition

A **view** provides a virtual representation of data from one or more tables. It is defined by a query.

```
CREATE VIEW ViewName [(AttributeList)] AS
SELECT ...;
```

Types:

- **Simple view:** selection and projection over one table.
- **Complex view:** includes joins, aggregates or other complex operations.

Example: orders whose amount exceeds 10,000:

```
CREATE VIEW MainOrder AS
SELECT *
FROM Orders
WHERE Amount > 10000;
```

Querying the view:

```
SELECT CustId
FROM MainOrder;
```

This is equivalent to composing the view definition with the query:

```
SELECT CustId
FROM Orders
WHERE Amount > 10000;
```

### 4.2 Cascaded views

A view can be defined using another previously defined view.

```
CREATE VIEW AdminEmployee (Id, Name, Surname, Salary) AS
SELECT Id, Name, Surname, Salary
FROM Employee
WHERE Dept = 'Admin'
  AND Salary > 10;

CREATE VIEW AdminJuniorEmployee AS
SELECT *
FROM AdminEmployee
WHERE Salary < 50;
```

The second view uses the first view as its source. In the SQL-2 model described by the slides, views can depend on previously defined views, but mutual dependency is not allowed.

### 4.3 Views for complex queries

Views can improve readability by decomposing a complex problem into smaller queries. They can also express queries involving nested aggregations.

#### Example: customer with the greatest total order amount

Without a view:

```
SELECT CustId
FROM Orders
GROUP BY CustId
HAVING SUM(Amount) >= ALL (
    SELECT SUM(Amount)
    FROM Orders
    GROUP BY CustId
);
```

Using a view:

```
CREATE VIEW CustAmount (CustId, TotAmt) AS
SELECT CustId, SUM(Amount)
FROM Orders
GROUP BY CustId;

SELECT CustId
FROM CustAmount
WHERE TotAmt = (
    SELECT MAX(TotAmt)
    FROM CustAmount
);
```

The view calculates each customer's total. The outer query then finds the maximum total.

#### Example: average number of orders per customer

A direct nested aggregate is not supported in the form shown in the slides:

```
-- Incorrect
SELECT AVG(COUNT(*))
FROM Orders
GROUP BY CustId;
```

Use a view to calculate each customer's order count first:

```
CREATE VIEW CustOrd (CustId, NumberOrders) AS
SELECT CustId, COUNT(*)
FROM Orders
GROUP BY CustId;

SELECT AVG(NumberOrders)
FROM CustOrd;
```

**Key idea:** calculate the inner aggregate in a view, then aggregate the resulting rows.

### 4.4 Modifying data through views

Simple views may allow updates to their underlying tables.

For example:

```
CREATE VIEW MainOrder AS
SELECT *
FROM Orders
WHERE Amount > 10000;
```

Then:

```
UPDATE MainOrder
SET Amount = Amount * 1.05
WHERE CustId = '45';
```

The operation can be interpreted against the underlying table with the view condition included:

```
UPDATE Orders
SET Amount = Amount * 1.05
WHERE CustId = '45'
  AND Amount > 10000;
```

This works for the simple view illustrated in the slides. For complex views involving joins or aggregates, it may not be possible to map modifications unambiguously back to the original data.

### 4.5 CHECK OPTION

A view can use `WITH CHECK OPTION` to restrict modifications.

```
CREATE VIEW MainOrder AS
SELECT *
FROM Orders
WHERE Amount > 10000
WITH CHECK OPTION;
```

A modification is accepted only if it does not cause the modified row to disappear from the view.

The slides distinguish:

- `LOCAL`: checks the relevant view's condition.
- `CASCADED`: checks conditions through underlying views as well.

---

## 5. Exam Essentials

- **DDL vs. DML:** DDL defines or modifies the database schema; DML inserts, deletes or updates rows.
    
- `**PRIMARY KEY**`**:** unique and not null; may contain multiple attributes.
    
- `**UNIQUE(A, B)**`**:** the pair must be unique, not necessarily each attribute separately.
    
- **Foreign keys:** preserve referential integrity between tables.
    
- `**CASCADE**`**,** `**SET NULL**`**,** `**SET DEFAULT**`**,** `**NO ACTION**`**:** know the effect of each policy on deletion and update.
    
- `**DELETE**` **vs.** `**DROP**`**:** `DELETE` removes rows; `DROP` removes a schema object.
    
- `**ALTER**`**:** modifies existing schema objects.
    
- `**INSERT**`**:** attribute/value order matters; omitted attributes receive defaults or `NULL`.
    
- `**UPDATE**`**:** multiple rows may change; command order can change the final result.
    
- `**GRANT**` **vs.** `**REVOKE**`**:** assign and remove privileges. `WITH GRANT OPTION` permits onward granting.
    
- **Views:** virtual query-based representations; useful for simplifying queries and handling nested aggregates.
    
- **Simple vs. complex views:** simple views are more likely to support data modifications.
    
- `**WITH CHECK OPTION**`**:** prevents modifications that make rows stop satisfying the view definition.
    
- **Nested aggregates:** when a direct nested aggregate is not supported, calculate the first aggregation in a view and aggregate its results.