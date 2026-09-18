## 1. Information Systems

**Information systems** are complementary networks of hardware and software components used by people and organizations to:

- collect data
    
- store data
    
- filter and process data
    
- create and distribute data/information
    
- support coordination, control, analysis and decision making
    

---

## 2. Data vs Information

### Data

A **data item** is a rough unit of information.

Example:

```text
<16/9/25, 13:30, 26.8, 1017.1, 3.8>
```

### Information

Information is the result of processing data to fulfil a specific need.

Processing involves:

- applying an interpretation
    
- answering a question/query
    

Data+Semantics=Information

---

## 3. Where Are Data Stored?

Traditionally, data are stored in **files**.

Problem:

- Does every application have its own files?
    
- How can different applications share the same data?
    
- How can inconsistencies be avoided?
    

A **DBMS** provides a common system between applications and stored data.

---

## 4. Database Management System (DBMS)

A **DBMS** is a complex software system designed to manage data.

Examples:

- SQL Server, Oracle, DB2, MySQL

A DBMS provides:

- An efficient, reliable, convenient, and safe multi-user storage of and access to massive amounts of persistent datasets.

---

## 5. Main Features of a DBMS

Important properties:

- **Massive** → handles large amounts of data
    
- **Persistent** → data remain stored
    
- **Safe** → protection against improper use
    
- **Multi-user** → concurrent access
    
- **Convenient** → easy data access
    
- **Efficient** → efficient queries/access
    
- **Reliable** → dependable storage/access
    

 **Data sharing**
- No unnecessary replication
- Concurrent access

 **Data quality**
- Integrity constraints

**Efficacy**
- Query
- Sort
- Report

**Access control**
- Privacy
- Robustness

---

## 6. DBMS vs File System

With a file system:

- applications need to understand the internal structure of files
- inconsistencies and incoherencies may occur

A DBMS provides a **data dictionary/catalog**:

- one unique description of the data
- available to all programs
- higher-level data access
- greater integration and flexibility

---

## 7. Data Sharing and Integration

Ideally, each data item is stored **only once**, independently of how many applications use it.

Benefits:

- no unnecessary redundancy
- no memory waste
- increased consistency
- ambiguity avoided by construction

Replication may still be used for **durability**, but it is controlled by the system.
Data also have multiple layers of protection against improper use.

---

## 8. Using a DBMS

A DBMS supports three main activities:
- **Data Definition Language (DDL)**
	- Used to define data structures.

- **Data Manipulation Language (DML)**
	- Used to modify the dataset.

- **Query Language**
	- Used to ask questions/queries about the data.

---

## 9. Schema vs Instance 

### Schema

The **structural description** of relations in a database.

Example:

```text
Student(Sid, Name, City, Dept)
```

### Instance

The **actual data content** of the database at a given point in time.

Schema=structure
Instance=current data

---

## 10. DDL Example

Example of defining a relation:

```sql
CREATE TABLE Student (
    Sid CHARACTER(3) PRIMARY KEY,
    Name VARCHAR(30) NOT NULL,
    City VARCHAR(20),
    Dept VARCHAR(3)
);
```

DDL defines the **schema**.

---

## 11. Query Language Example

Example:

```sql
SELECT Name, Dept
FROM Student
WHERE City = 'Bologna';
```

This asks for:

> students from Bologna, returning their name and department.

---

## 12. DML

DML performs modifications.

### Schema modifications

```text
ALTER
ADD
DROP
```

### Instance modifications

```text
INSERT
DELETE
UPDATE
```

---

## 13. Null Values

`NULL` is a special value representing:

- unknown, undefined, missing information, uncertainty, incompleteness, ignorance, inapplicability.

Properties:

- polymorphic → compatible with all data types/domains.
- represents absence of information.
- null values cannot be distinguished from one another.
- however, null values are all different from one another.

---

## 14. Users of a DBMS

### Database Administrator

- defines/manages database structures
- uses DDL
### Application Developers

- develop applications
- use DML
### Occasional Users

- perform queries
- use query languages or graphical interfaces
### Final Users

- use the applications

---
# Exam Essentials

- **Data item** → rough unit of information
    
- **Information** → data + semantics / processing for a specific need
    
- **DBMS** → software system for managing databases
    
- DBMS → efficient, reliable, convenient, safe, multi-user access
    
- DBMS → manages massive amounts of persistent data
    
- **Data sharing** → avoid unnecessary redundancy
    
- **Data quality** → integrity constraints
    
- **DDL** → define data structures/schema
    
- **DML** → modify data/instance
    
- **Query language** → ask questions about data
    
- **Schema** → structure
    
- **Instance** → actual data at a given time
    
- `CREATE TABLE` → DDL
    
- `INSERT`, `DELETE`, `UPDATE` → DML
    
- `ALTER`, `ADD`, `DROP` → schema modifications
    
- **NULL** → unknown/undefined/missing information
    
- DBMS users → administrator, developers, occasional users, final users
    
- **Big Data** → very large datasets
    
- **NoSQL** → semi-/unstructured data