## 1. Abstraction in Conceptual Models

Abstraction is the act of identifying and describing properties shared by sets of real-world objects or facts.

Three fundamental abstractions are used in knowledge representation:

- Classification
- Aggregation
- Generalization

## 2. Classification

Classification is the process of defining classes of items from the real world, including objects and facts.

Examples:

- Books
- Motorbikes
- Persons
- Contracts

Each class has an implicit membership test that determines whether a real-world item is an instance of that class.

An item can belong to multiple classes simultaneously.

Example: A chair can be classified as both white furniture and red furniture only if it satisfies the corresponding classifications (depending on the model's definitions).

## 3. Aggregation

Aggregation builds a complex class by combining simpler classes, called components.

Example: A `Bicycle` is composed of:

- 2 Wheels
- 2 Pedals
- 1 Frame
- 1 Handlebar

Each component is annotated with the number of instances required to build an instance of the aggregated class.

- If no number is specified, the default is 1.

## 4. Generalization

Generalization establishes subset relationships between classes.

Examples:

- `Mother ⊂ Woman ⊂ Person`
- `Man ⊂ Person`

A class can be a specialization of another, more general class.

### Inheritance

Subsets inherit all the properties of their supersets.

Example:

- `Person`: Name, Surname, BirthDate, Address
- `Employee`: inherits Person's properties and adds Salary and ContractType.
- `Student`: inherits Person's properties and adds MatriculationNo and Track.

## 5. The Entity-Relationship (E-R) Model

The E-R Model (Entity-Relationship Model) was proposed by Peter Chen in 1976.

It is widely used as an industrial standard in conceptual database design.

It defines two fundamental concepts:

- Entities: classes of relevant items.
- Relationships: associations between entities.

### 5.1. Entities

An Entity represents a class of items relevant to the application.

Entities can represent:

- Objects: cars, students, employees.
- Facts: bank accounts, university courses, contracts.

Rules:

- Every entity must be relevant to the application.
- Each entity must have a unique name within the model.

Visual notation: A rectangle containing the entity name.

Example: `Student`.

### 5.2. Relationships (Associations)

A Relationship represents a logical binding between entities.

Each instance of a relationship is an n-tuple containing instances of the participating entities.


For a relationship between n entities:

$$
R = \{ \langle e_1, \ldots, e_n \rangle \}
\subseteq E_1 \times \cdots \times E_n
$$

Example: the relationship between a car $c$ and its owner $o$ is represented by the pair:

$$
\langle c, o \rangle
$$

Rules:

- A relationship can involve two or more entities.
- Each relationship has a unique name within the model.

Visual notation: A diamond containing the relationship name, connected to the participating entities.

Example: `Student — pass — Exam`.

### 5.3. Attributes

Attributes represent characteristics of entities or relationships that are relevant to the application.

Examples:

- `Student`: BirthDate, Name.
- `pass`: Grade.

Each attribute has a name that must be unique within its entity (or relationship).

### 5.4. Guidelines for choosing E-R constructs

|Concept|E-R construct|When to use it|
|---|---|---|
|Intrinsically relevant concept|Entity|It represents an important object or fact in the application.|
|Elementary measure or value|Attribute|It describes a characteristic of an entity or relationship.|
|Binding between entities|Relationship|It expresses an association between entities.|

Important: The mapping between real-world concepts and E-R constructs depends on the application context. It is not predefined.

Example: `Color` can be:

- An attribute of `Car`, when describing the color of a particular car.
- An entity, when modelling colors themselves and their properties, such as wavelength.

## 6. Cardinality of Associations

An instance of an entity can participate in the same association multiple times.

The model must explicitly specify the minimum and maximum number of participations allowed for each entity.

Cardinality is expressed as:

\[ (\text{MIN-CARD},\text{MAX-CARD}) \]

### 6.1. Minimum and maximum cardinality

|Value|Meaning|
|---|---|
|MIN = 0|Optional participation|
|MIN = 1|Mandatory participation|
|MAX = 1|At most one participation|
|MAX = N|Any number of participations (many)|

Specific values other than 0, 1 or N are possible, but rare.

### 6.2. Types of associations

Associations are classified according to their maximum cardinalities.

#### One-to-one (1:1)

Example: `Employee — management — Department`

- An employee may manage at most one department: `(0,1)`.
- A department must have exactly one manager: `(1,1)`.

#### One-to-many (1:N)

Example: `Shelf — contains — Item`

- A shelf can contain zero or many items: `(0,N)`.
- An item can belong to at most one shelf: `(0,1)`.

#### Many-to-many (N:M)

Example: `Project — signature — Engineer`

- A project must be signed by at least one engineer: `(1,N)`.
- An engineer may have signed any number of projects: `(0,N)`.

Important: The `N` on both sides does not mean the maximum number must be the same. It simply means that the maximum cardinality is unbounded.

## 7. Self-Associations

A self-association (or recursive association) occurs when an entity is related to other instances of the same entity.

The roles of the participating instances can be specified to distinguish their meanings.

### 7.1. One-to-one self-association

Example: An `Employee` can be related to another employee through a relationship representing marriage.

Roles:

- Husband: `(0,1)`
- Wife: `(0,1)`

Each employee can participate in at most one relationship in each role.

### 7.2. One-to-many self-association

Example: `Employee — reportsTo — Employee`

Roles:

- Boss: `(0,N)` — one boss can supervise many employees.
- Subject: `(0,1)` — an employee can report to at most one boss.

### 7.3. Many-to-many self-association

Example: `Component — equivalence — Component`

Roles:

- Replaces: `(0,N)`
- Replaced_by: `(0,N)`

A component can replace many components and can itself be replaced by many others.

## 8. Ternary Associations

A ternary association connects three entities simultaneously.

Example: `Driver — drives — Bus — Route`.

The relationship represents a combination of:

\[ \langle Driver, Bus, Route\rangle \]

The association must be considered as a whole: its instances are combinations of the three participating entities.

### 8.1. Why ternary relationships matter

Consider public transport. A driver can drive a bus on a route.

If the same driver can drive the same bus on the same route multiple times, on different dates or at different times, the combination:

\[ \langle Driver, Bus, Route\rangle \]

would need to appear more than once in the relationship.

This is not possible when the relationship is modelled as a set of tuples.

Solution: Introduce an additional entity, such as `Journey`, to represent each individual trip.

The journey can then be associated with:

- The driver.
- The bus.
- The route.
- The date and time.

This allows multiple journeys with the same driver, bus and route.

### 8.2. When a simpler model is valid

A ternary association can sometimes be replaced with simpler binary associations, but only if the additional constraints make the model equivalent.

Example: Modelling `Driver — drives — Bus` and `Bus — on — Route` is valid only if each bus can operate on just one specific route.

Important: Replacing a ternary association with binary associations can lose information if the original three-way dependency is not preserved.

## 9. Cardinality of Attributes

Attributes can be classified according to their structure and number of values.

### 9.1. Scalar attributes

A scalar attribute has exactly one value.

Examples:

- StudentId
- Surname
- Grade

### 9.2. Multiple attributes

A multiple attribute can have several values.

Examples:

- PhoneNo
- Diploma
- Skill

Attribute cardinality is also expressed using `(MIN, MAX)`.

If no cardinality is specified, the default is: (1,1) 

### 9.3. Composite attributes

A composite attribute consists of multiple simpler components.

Examples:

- `Date`: day, month, year.
- `Address`: city, street, number, zipcode.

### 9.4. Composite multiple attributes

A composite multiple attribute combines both properties:

- It contains multiple components.
- It can have multiple instances.

Examples:

- `Account`: URL, username, password.
- `Address`: when a person can have multiple addresses.

### 9.5. Optional attributes

An attribute is optional when the absence of a value is acceptable.

Examples:

|Cardinality|Meaning|Example|
|---|---|---|
|`(0,1)`|Zero or one value|DrivingLicence|
|`(0,N)`|Zero or many values|PhoneNo, Diploma|

Important: The default `(1,1)` applies to attributes only. Association cardinalities must always be explicitly specified.

## 10. Identifiers

An identifier uniquely characterizes each individual instance of an entity.

It is similar to a key in the logical database model.

Identifiers are typically not updatable, or are changed very rarely.

Examples:

- `Citizen`: SSN.
- `Car`: PlateNo.
- `Book`: ISBN.

### 10.1. Composite identifiers

An identifier can consist of multiple attributes.

Examples:

- `Hotel`: name + city.
- `City`: name + country.
- `Plant`: name + company + address.

A composite identifier must be minimal: no component can be removed without losing its ability to uniquely identify the entity.

### 10.2. Multiple identifiers

An entity can have more than one identifier.

Examples:

- `Car`: PlateNo, FrameNo.
- `Student`: MatriculationNo, Email.

Other attributes, such as Model, ProductionNo, BirthPlace, BirthDate and FullName, may also describe these entities but are not necessarily identifiers.

## 11. Weak Entities

A weak entity represents an object or fact that can exist in the application context only if another, stronger entity exists.

Its identification depends on the identifier of the stronger entity.

Therefore, the identifier of the strong entity is required to identify the weak entity.

### 11.1. Examples

Book and Chapter

- `Book` is the strong entity, identified by ISBN.
- `Chapter` is weak and has its own chapter number (`num`).
- A chapter is identified using the book identifier together with its chapter number.

Train and Seat

- `Train` is the strong entity.
- `Seat` is weak and identified in relation to its train.

Hotel and Room

- `Hotel` is the strong entity.
- `Room` is weak and identified in relation to its hotel.

### 11.2. Weakness and deletion

Weak entities depend on their stronger entities.

Deleting a strong entity also causes the deletion of its associated weak entities.

Examples:

- Removing a train car → its seats are removed.
- Closing a hotel floor → its rooms are removed.

Key idea: A weak entity cannot be identified independently of its owning strong entity.

## 12. Generalization Hierarchies

A generalization hierarchy connects a parent (father) entity \(E\) with child entities \(E_1,\ldots,E_n\).

For each child entity \(E_i\):

- Every instance of \(E_i\) is also an instance of \(E\).
- Every instance of \(E\) can potentially be an instance of \(E_i\).

Terminology:

- Generalization: The parent entity.
- Specialization: The child entities.

### 12.1. Inheritance

Child entities inherit all properties of their parent entities:

- Attributes.
- Relationships.
- Identifiers.

Example:

`Personnel` is specialized into:

- `Employee`: adds Salary.
- `Consultant`: adds DailyCost.

Both inherit the common properties of `Personnel`, such as id, name and phone number.

## 13. Properties of Generalization Hierarchies

Two independent properties characterize a generalization hierarchy:

- Total (t) / Partial (p)
- Exclusive (e) / Overlapping (o)

### 13.1. Total vs. Partial

Total (t): Every instance of the parent entity must belong to at least one child entity.

Example: `Personnel` consists entirely of employees or consultants.

Partial (p): An instance of the parent entity does not necessarily have to belong to any child entity.

Example: A `Citizen` may be neither a hunter nor a fisher.

### 13.2. Exclusive vs. Overlapping

Exclusive (e): An instance of the parent entity cannot belong to more than one child entity.

Example: A member of `Personnel` cannot simultaneously be an employee and a consultant.

Overlapping (o): An instance of the parent entity can belong to multiple child entities.

Example: A `Citizen` can be both a hunter and a fisher.

### 13.3. Combination of properties

|Property|Meaning|
|---|---|
|`(t,e)`|Total and exclusive|
|`(t,o)`|Total and overlapping|
|`(p,e)`|Partial and exclusive|
|`(p,o)`|Partial and overlapping|

Default properties: `(t,e)`.

If a hierarchy uses different properties, they must be explicitly indicated.

Examples:

- `Personnel → Employee, Consultant`: `(t,e)`.
- `Citizen → Hunter, Fisher`: `(p,o)`.

## 14. Further Specializations

An entity that is already a child in a hierarchy can itself be specialized.

Example:

`Consultant` can be specialized into:

- Lawyer
- Engineer
- Accountant

This hierarchy can be `(p,e)`:

- Partial: Consultants may have other qualifications not represented by a child entity.
- Exclusive: A consultant is not considered to belong to multiple specializations simultaneously in this application.

Another example:

`Expert` can be specialized into:

- Logistics
- Mechanics
- Electronics

This hierarchy is `(p,o)`:

- Partial: Experts may have other disciplines.
- Overlapping: An expert can be qualified in multiple disciplines.

Important: The choice of hierarchy properties depends on the application domain and its requirements, not only on what is theoretically possible.

## 15. Multiple Inheritance

Multiple inheritance allows an entity to inherit properties from multiple parent entities or to represent the intersection of different specializations.

Example:

`Citizen` can be specialized into:

- Student
- Worker
- Retired

An additional intersection subclass can be defined:

`Student-Worker`

This represents citizens who belong to the Student and Worker categories and are explicitly considered part of their intersection.

Important distinction: Not every citizen who is both a student and a worker must necessarily belong to the `Student-Worker` subclass. The subclass can represent an additional property, such as voluntary registration in that category.

## 16. Complex Generalization Examples

### 16.1. Industrial vehicles

A general entity `Vehicle` can be specialized into:

- Three-wheeler
- Truck
- Car
- Forklift

`Truck` can be further specialized into:

- Van
- Box
- Semi-trailer

This illustrates multiple levels of specialization.

### 16.2. Academic personnel

`Personnel` is specialized into:

- `Staff`
- `Faculty`

Both hierarchies are `(p,e)`.

Further specializations:

- `Staff` → Technician, Admin.
- `Faculty` → Full, Associate, Assistant.

Each child inherits the properties of `Personnel`.

### 16.3. Company personnel

A company can have:

- `Employee`: with Salary.
- `Consultant`: with DailyCost.

Both inherit common properties from `Personnel`, such as id and name.

The model can also define relationships such as:

- `Clerk` representing an employee's role.
- `Manager` representing a consultant's role.
- `rep` and `supervision` to represent relationships between personnel.
- `task` to represent tasks assigned to personnel, with an associated Level.

# Exam Essentials

## Fundamental abstractions

- Classification: Defines classes of real-world objects or facts.
    
- Aggregation: Builds complex classes from simpler components.
    
- Generalization: Establishes subset relationships and inheritance.
    

## E-R model

- Entity: Represents a relevant class of objects or facts.
    
- Relationship: Represents a binding between entities.
    
- Attribute: Represents a characteristic of an entity or relationship.
    
- The choice between entity, relationship and attribute depends on the application context.
    

## Association cardinalities

|Cardinality|Meaning|
|---|---|
|`(0,1)`|Optional, at most one|
|`(1,1)`|Exactly one|
|`(0,N)`|Optional, many|
|`(1,N)`|Mandatory, many|

- 1:1 = one-to-one.
    
- 1:N = one-to-many.
    
- N:M = many-to-many.
    
- `N` on both sides does not imply equal maximum values.
    
- Self-associations connect instances of the same entity.
    
- Ternary associations connect three entities simultaneously.
    

## Attribute types

- Scalar: One value.
    
- Multiple: Several values.
    
- Composite: Made of simpler components.
    
- Composite multiple: Multiple instances, each with several components.
    
- Default attribute cardinality: `(1,1)`.
    
- Optional attributes can use `(0,1)` or `(0,N)`.
    

## Identifiers and weak entities

- An identifier uniquely identifies an entity instance.
    
- Composite identifiers must be minimal.
    
- An entity can have multiple identifiers.
    
- A weak entity depends on a strong entity for identification.
    
- Deleting the strong entity also deletes its dependent weak entities.
    

## Generalization hierarchies

- Children inherit attributes, relationships and identifiers from their parents.
    
- Total (t): Every parent instance belongs to at least one child.
    
- Partial (p): Some parent instances may belong to no child.
    
- Exclusive (e): An instance belongs to at most one child.
    
- Overlapping (o): An instance may belong to multiple children.
    
- Default hierarchy: `(t,e)`.
    
- Multiple inheritance and intersection subclasses can represent combinations of specializations.