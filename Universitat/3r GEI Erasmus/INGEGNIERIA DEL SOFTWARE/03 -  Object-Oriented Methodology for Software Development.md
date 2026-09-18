## 1. Software Reuse

> **Reuse:** using previously developed components, ideas or designs instead of developing everything from scratch.

Software development should follow a similar principle to industrial production:

- Reuse existing components.
    
- Assemble components developed internally or externally.
    
- Reuse previously developed ideas and designs.
    

### Benefits of Reuse

- Reduced **development time**.
    
- Less **maintenance**.
    
- Greater **robustness and reliability**.
    
- Greater **efficiency**.
    
- Preserve and exploit company **know-how**.
    

### Resistance to Reuse

**Technical problems**

- Reusable modules must be **adaptable**.
    

**Non-technical problems**

- Fear of relying on code developed by others.
    
- Fear of losing efficiency.
    
- Short-term projects with limited resources.
    
- Difficulty managing a growing number of reusable components.
    

---

# 2. Modularization

> **Modular system:** a system divided into parts with substantial individual autonomy and limited interaction with other parts.

Good modules should be:

- **Loosely coupled** → limited dependencies/interactions.
    
- **Highly cohesive** → elements within a module strongly belong together.
    

A module is a provider of computational resources:

- Procedures
    
- Data structures
    
- Types
    

### Interface vs Implementation

For a module, distinguish:

**What it does**

- Set of exported services.
    
- **Interface**
    

**How it is implemented**

- Internal details.
    
- **Implementation**
    

> The **interface is the contract** between a module and its clients.

### Why Modularize?

Main motivation:

> **Reduce complexity** → _divide et impera_ (divide and conquer).

Modularization is also a **precondition for code reuse**.

---

# 3. Traditional Top-Down Approach

> **Top-down functional decomposition:** recursively decompose the main functionality of a system into simpler functionalities.

Process:

1. Start from the main functionality.
    
2. Decompose it into simpler functions.
    
3. Continue until functions are simple enough to implement directly.
    
4. Divide implementation work among programmers based on the identified functionalities.
    

Typical modules:

- Procedures
    
- Libraries
    
- Data pools
    

### Advantages

- Ordered.
    
- Logical.
    
- Disciplined.
    
- Helps control complexity.
    
- Suitable for designing **algorithms**.
    

### Limitations

Not well suited to **large systems** because:

- A system may not have a single main functionality.
    
- System functionalities can change frequently.
    
- Module dependencies are decided **too early**.
    

---

# 4. Procedural vs Object-Oriented Abstraction

An alternative to functional decomposition is to use abstraction.

### Procedural Abstraction

- Libraries
    
- Common data pools
    

### Object-Oriented Abstraction

- Abstract objects
    
- Abstract Data Types (**ADT**)
    

---

# 5. Data Types

> **Type = set of values + set of operations**

Example: `int`

**Values:**

```text
..., -2, -1, 0, 1, 2, ...
```

**Operations:**

```text
+, -, *, /, <, ==, ...
```

Operations apply to entities (objects) of that type.

### User-Defined Types

Type constructors can create new composite types:

- Arrays
    
- Structures (`struct`)
    
- etc.
    

Complex data structures can therefore be built from simpler types.

### Benefits of Types

Types:

- Classify data.
    
- Abstract away the underlying memory representation.
    
- Protect against illegal operations through **type checking**.
    

Example:

```text
Data d;
int g;

d = g;   // illegal
```

Type errors can be detected by the **compiler**, without executing the program.

---

# 6. The Data Representation Problem

Suppose one programmer defines a `Data` type and another programmer uses it.

If the user directly accesses the internal representation:

```c
d.giorno
d.mese
d.anno
```

then changing the internal representation of `Data` requires modifications throughout the program.

### Problem

A small change to a widely used data structure can cause:

- Many code modifications.
    
- More errors.
    
- More development time.
    
- Higher cost.
    
- Difficult maintenance, especially with poor documentation.
    

> The internal implementation of data structures is one of the parts most subject to change.

---

# 7. Encapsulation

> **Encapsulation:** restrict direct access to the internal representation of a data structure and provide controlled operations to access/manipulate it.

Instead of directly accessing fields, clients use predefined operations:

```c
inizializzaData(&d, 14, 12, 2011);

if (leggiGiorno(d) == 27)
    pagaStipendi();

if (leggiMese(d) == 12)
    pagaTredicesime();
```

If the internal representation changes:

- The implementation of the operations changes.
    
- The **client code does not change**.
    

### Main Benefit

Changes remain confined to the data structure.

→ Modifications become:

- Simpler.
    
- Faster.
    
- Less expensive.
    

---

# 8. Abstract Data Type (ADT)

> **Abstract Data Type (ADT):** abstraction based on the **expected behaviour** of data rather than its implementation/representation.

An ADT is defined by:

- A set of values.
    
- A set of operations applicable to those values.
    
- An **interface** specifying those operations.
    

### Information Hiding

Only the operations defined by the interface can be used to:

- Create objects.
    
- Modify objects.
    
- Access objects.
    

The implementation is **hidden** from clients.

> **ADT = specification/interface + hidden implementation**

Different implementations can provide the same ADT.

Example:

```text
Data
 ├── Implementation A
 │    day + month + year
 │
 └── Implementation B
      day + month-name + year
```

The client still uses:

```text
mese(d)
```

without knowing how the month is internally represented.

---

# 9. Designing an ADT Interface

The interface is a **promise to clients**:

> Operations will remain valid even if the implementation changes.

The interface should contain **all operations useful to clients**.

For example, if clients need to calculate the day after a date, the ADT should provide an appropriate operation rather than forcing clients to access internal fields.

---

# 10. ADTs in C

C has no dedicated language constructs for ADTs.

However, ADTs can be approximated using:

- Preprocessor
    
- Header files
    
- Pointers
    
- Function prototypes
    
- Discipline
    

This allows separation of:

- **Interface**
    
- **Implementation**
    

Object-oriented languages provide additional abstractions useful for **programming-in-the-large**.

---

# 11. Object-Oriented Languages

Object-oriented languages:

- Encapsulate data structures inside **classes**.
    
- Can restrict access to implementation details.
    
- Allow concrete **objects/instances** to be created from abstractions.
    

### Constructors and Methods

Methods are procedures that:

- Belong to objects.
    
- Operate implicitly on the object to which they belong.
    

---

# 12. Objects

> **Object:** a concept, abstraction or entity with a well-defined meaning for the application.

An object can represent:

- A **physical** entity.
    
- A **conceptual** entity.
    
- A **software** entity.
    

Every object has three fundamental characteristics:

### State

Defined by its **attributes**.

### Behaviour

Defined by its **methods/operations**.

### Identity

A unique identity distinguishing the object from others.

> **Object = State + Behaviour + Identity**

An object can also be composed of other objects.

---

# 13. Object State

> **State:** the condition in which an object currently exists.

The state is defined by the values of its **attributes**.

Attributes can be:

- **Mutable** → can change.
    
- **Immutable** → do not change.
    

Example:

```text
Car
 ├── license plate → mutable
 ├── colour → immutable
 ├── chassis → immutable
 └── engine state → mutable
```

---

# 14. Object Behaviour

> **Behaviour:** determines how an object acts and reacts.

It includes:

- Changes in object state.
    
- Reactions to actions/messages from other objects.
    

Behaviour is defined by the set of **operations/methods** the object can perform.

---

# 15. Object Identity

> **Identity:** unique identity associated with each object.

Two objects can be:

### Equal

They have the **same state/value**.

### Identical

They are the **same object** (same identity).

> **Equal ≠ Identical**

---

# 16. Classes

> **Class:** a group of entities sharing a set of common characteristics.

A class is a **model/template** used to create objects with similar properties.

> Every object is an **instance of a class**.

A class is an abstraction of reality:

- Emphasizes relevant characteristics.
    
- Ignores irrelevant properties.
    

### Example: Course

**State / Attributes**

- Name
    
- Identifier
    
- Professor
    
- Classroom
    
- Semester
    

**Behaviour / Methods**

- `AddStudent`
    
- `RemoveStudent`
    
- `ListStudents`
    
- `CourseFull`
    

---

# 17. One Class = One Abstraction

> Each class should capture **one and only one abstraction**.

### Incorrect

A `Student` class containing:

- Student personal data.
    
- Courses in which the student is enrolled.
    

### Correct

Separate abstractions:

```text
Student  ───── relation ───── Course
```

→ One class for `Student` and one for `Course`.

---

# 18. Class Encapsulation

Class properties are divided into:

### Public

- Mainly **methods**.
    
- Accessible through the public interface.
    

### Private

- Mainly **attributes**.
    
- Hidden from users.
    

Private properties can only be accessed/modified through appropriate public operations.

> From the user's perspective, objects are **black boxes**.

```text
        Public Interface
              ↓
        ┌─────────────┐
        │    Object   │
        │             │
        │  private    │
        │  attributes │
        │             │
        └─────────────┘
```

---

# 19. Classes and Objects

> **Object = instance of a class.**

A class:

- Describes a set of objects.
    
- Defines their common structure/properties.
    
- Defines their behaviour.
    
- Acts as a **template** for creating objects.
    

---

# 20. Class Definition

A class can define:

- Class name.
    
- Instance attributes:
    
    - Name and type.
        
- Class attributes:
    
    - Name and type.
        
- Instance methods.
    
- Class methods.
    

> Classes are a construct for defining **Abstract Data Types**.

---

# 21. Messages and Methods

Objects in an application communicate through **message passing**.

When an object receives a message:  
→ A corresponding **method** is invoked.

```text
Object A
   │
   │ message
   ↓
Object B
   │
   ↓
method()
```

---

# 22. Relationships Between Classes

Main relationships presented in the course:

### Inheritance

> **is-a / kind-of**

Represents specialization/generalization.

Example:

```text
Automobile
     ↑
     │ is-a
Electric Automobile
```

### Aggregation / Containment

> **part-of / has-a**

Example:

```text
Automobile
    │
    └── has-a → Steering Wheel
```

### Use

Objects/classes interact through **message passing**.

---

# 23. Inheritance

> **Inheritance:** relationship where a subclass shares/inherits structure and/or behaviour from one or more superclasses.

Terminology:

```text
Superclass
    ↑
    │ inherits
    ↓
Subclass
```

Types:

- **Single inheritance** → subclass inherits from one superclass.
    
- **Multiple inheritance** → subclass inherits from multiple superclasses.
    

### Example

```text
Vehicle
   ↑
 Truck
```

All trucks are vehicles, but not all vehicles are trucks.

### Property Distribution

Properties should be defined at the **highest possible level** of the hierarchy.

Subclasses:

- Inherit properties.
    
- Can add new properties.
    
- Can modify existing properties.
    

---

# 24. Multiple Inheritance

> A class inherits from more than one superclass.

The inheritance hierarchy becomes an **acyclic graph**.

### Advantages

- Flexibility.
    
- Code reuse.
    

### Problems

**Name ambiguity**

- Two superclasses may define properties with the same name.
    

**Method lookup efficiency**

- Searching becomes more complex because the hierarchy is a graph rather than a simple tree.
    

Possible solutions depend on the programming language:

- Name resolution mechanisms.
    
- Caching.
    

### Common Ancestor Problem

If two parent classes inherit from a common ancestor containing attribute `x`, the child may face the question of whether it should contain:

- One copy of `x`, or
    
- Two copies of `x`.
    

Different languages handle this differently.

---

# 25. Generalization vs Specialization

### Generalization

Create a **superclass** containing properties common to several classes.

Process:

1. Identify similarities.
    
2. Create a superclass.
    
3. Original classes become subclasses.
    

```text
       Common
      /      \
   Class A  Class B
```

### Specialization

Create a **subclass** representing objects with particular behaviour/properties.

Process:

1. Identify instances with special behaviour.
    
2. Define a subclass for them.
    

---

# 26. Inheritance — Advantages & Disadvantages

### Advantages

- Natural characterization of objects.
    
- **Code reuse**.
    
- **Extensibility**.
    

### Disadvantages

- Possible efficiency problems, especially with **dynamic binding**.
    
- Weak locality.
    
- Potential conflict with **static typing**.
    
- Can affect code **verifiability and analyzability**.
    

---

# 27. Inheritance vs Containment

These relationships are often confused.

### Inheritance

> **is-a / kind-of**

Example:

```text
ElectricCar is-a Car
```

### Containment

> **part-of / has-a**

Example:

```text
Car has-a SteeringWheel
```

Think:

> **Inheritance = BE**  
> **Containment = HAVE**

---

# 28. Polymorphism

> **Polymorphism:** ability of an entity to assume different forms during its existence.

A polymorphic variable can refer to objects of different types.

### Static Type

Known at **compile time**.

Defines the general characteristics of objects that the variable can refer to.

### Dynamic Type

Known at **run time**.

Defines the specific type of object referenced at a given moment.

Example:

```text
static type:  Automobile
dynamic type: ElectricAutomobile
```

A variable of static type `Automobile` can refer to an instance of `ElectricAutomobile` when inheritance is present.

---

# 29. Late / Dynamic Binding

### Static Binding

The association between a method name and the code to execute occurs at:

> **Compile time**

### Late / Dynamic Binding

The association occurs at:

> **Run time**, when the method is called.

This allows different subclasses to provide different implementations of the same method.

### Example

Instead of:

```text
if object == Rectangle
    draw rectangle
else if object == Triangle
    draw triangle
else if object == Circle
    draw circle
```

Object-oriented approach:

```text
figure[i].draw()
```

The appropriate `draw()` method is selected according to the object's **dynamic type**.

---

# 30. Polymorphism + Inheritance + Late Binding

These concepts work together:

```text
        Figure
       /  |   \
 Circle Rectangle Triangle
```

A variable of type `Figure` can reference any subclass.

Calling:

```text
figure.draw()
```

causes the appropriate subclass implementation to execute.

→ This avoids explicitly checking the object's type.

---

	# 31. Extensibility

Object-oriented design can make systems easier to extend.

Example:

Initially:

```text
Figure
 ├── Circle
 ├── Rectangle
```

Then:

```text
Figure
 ├── Circle
 ├── Rectangle
 ├── Triangle
```

The code can simply use:

```
figure[i].draw()
```

A new subclass can be added without changing the general iteration logic.

> **Extensibility:** ability to add new specialized behaviour while minimizing changes to existing code.

# 32. Terminology

### Abstract Data Type (ADT)

A data type consisting of:

- A set of values.
- Operations to access/modify instances.

### Module

A collection of related data and code definitions.

### Static Typing

Type information is associated with **identifiers**.

### Dynamic Typing

Type information is associated with **objects**; identifiers can refer to objects of different types.

### Scope

The region of a program where an identifier's definition is effective.

### Visible Identifier

An identifier accessible within its **scope**.

### Lifetime / Extent

The period during which an object **exists**.

### Inheritance

Ability of an ADT/type to inherit data and code from another ADT/type.

---

# 33. Object-Oriented Execution

An executing OO program consists of a set of **objects**.

Objects:

- Belong to **classes**.
- Contain **attributes** representing data/state.
- Contain **methods** representing behaviour.
- Communicate by sending **messages**.
- Respond to messages by executing methods.

Classes:

- Statically define the data and behaviour of a set of objects of the same type.

---

# Exam Essentials

- **Reuse** → reuse existing components, ideas and designs.
- Benefits of reuse: reduced development time/maintenance, greater robustness/reliability/efficiency, preservation of know-how.
- **Modularization** → divide a system into autonomous, loosely coupled and highly cohesive modules.
- Main reason for modularization: **reduce complexity**.
- A module has an **interface** and an **implementation**.
- **Interface = contract** between module and clients.
- **Top-down decomposition** → recursively decompose functionality; useful for algorithms but problematic for large, changing systems.
- **Type = values + operations**.
- **Encapsulation** → hide implementation and control access through operations/interface.
- **ADT** → defined by its behaviour/interface, not its representation.
- ADT provides **information hiding**.
- **Object = state + behaviour + identity**.
- **State** → attributes.
- **Behaviour** → methods/operations.
- **Identity** → unique object identity.
- **Equal ≠ identical**.
- **Class** → template/model for objects.
- **Object = instance of a class**.
- A class should capture **one and only one abstraction**.
- **Public methods** provide the interface; **private attributes** hide implementation.
- Objects communicate through **message passing**.
- **Inheritance = is-a / kind-of**.
- **Containment = has-a / part-of**.
- Inheritance supports **reuse and extensibility**.
- **Generalization** → factor common properties into a superclass.
- **Specialization** → create subclasses with refined behaviour.
- Multiple inheritance provides flexibilityand reuse advantatges but can cause **ambiguity and efficiency problems**.
- **Polymorphism** → entity can take different forms.
- **Static type** → compile-time.
- **Dynamic type** → run-time.
- **Static binding** → method selected at compile-time.
- **Dynamic/late binding** → method selected at run-time.
- **Inheritance + polymorphism** allow a superclass variable to refer to subclass objects.
- Late binding allows code such as `figure[i].draw()` without manually checking the object's type.
- OO execution: **objects communicate through messages and respond by executing methods**.