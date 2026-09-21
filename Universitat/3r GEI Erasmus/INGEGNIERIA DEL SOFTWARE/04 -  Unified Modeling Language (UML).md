## 1. Modeling

- A **model** is an abstract description of a system, including a description of the environment in which the system operates.
- Modeling helps analysts:
    - Understand system functionalities.
    - Communicate with stakeholders/clients.
- Different models present the system from different perspectives.

### Characteristics of Models

- Models are **abstractions**.
- They ignore details that may distract from the essence of the problem.
- Conclusions drawn from a model must take into account the approximation introduced by the abstraction.

---

# 2. UML Diagrams

UML diagrams can be divided into three main groups:

### Structure Diagrams

Describe the static structure of the system.

- Class diagrams
- Object diagrams
- Component diagrams
- Composite structure diagrams
- Package diagrams
- Deployment diagrams

### Behavior Diagrams

Describe system behavior.

- Use case diagrams
- Activity diagrams
- State machine diagrams

### Interaction Diagrams

Describe interactions between objects.

- Sequence diagrams
- Communication diagrams
- Timing diagrams
- Interaction overview diagrams

---

# 3. Use Case Diagrams

## Purpose

Use case diagrams are **behavioral diagrams** that describe the **requirements of a system**.

They describe:

- Which functionalities the system must provide.
- Who uses those functionalities.

They do **not** describe how the functionalities are implemented.

---

# 4. Class Diagrams

Class diagrams provide a **static description** of the system.

UML allows entities to be described with increasing levels of detail.

- A high level of detail can be inappropriate or counterproductive when specifying requirements.
- A high level of detail becomes essential when describing the architecture of the solution.
- At the implementation level, UML classes can correspond directly to classes in Java.

## Class Structure

A class consists of three main parts:

1. **Name**
2. **Attributes** → state
3. **Methods** → behavior

### Attribute

```
visibility name: type [multiplicity] = default {property string}
```

### Method

```
visibility name(parameter list): return type {property string}
```

### Visibility

|Symbol|Meaning|
|---|---|
|`+`|public|
|`-`|private|
|`#`|protected|
|`~`|friendly|

### Parameter

```
name: type = default
```

---

# 5. Associations

An **association** represents a relationship between classes.

Example:

```
Persona -------- works for -------- Azienda
```

An association can have:

- A **name**, usually a verb.
- **Roles** played by the classes in the association.
- Association **ends**.

Association ends are:

- **Implicit attributes**
- Have visibility like normal attributes.
- Have a multiplicity.

### Multiplicity

Examples:

```
1
0..1
1..*
4
6-12
```

Multiplicity specifies how many objects can participate in the relationship.

---

# 6. Association Classes

An **association class** is used when an association itself needs to have attributes or additional information.

Example:

```
Persona -------- Prestito -------- Banca
```

`Prestito` can contain information about the relationship, such as:

```
ammontare
rata
dataInizio
dataFine
```

The relationship therefore has its own class representation.

---

# 7. Aggregation

An **aggregation** is a particular form of association representing a **part-of relationship**.

```
Whole ◇-------- Part
```

- One object is related to another as one of its parts.
- It represents a whole/part relationship.

---

# 8. Composition

A **composition** is a **strong aggregation**.

Characteristics:

- Component parts **cannot exist without the container**.
- Creation and destruction of the parts occur within the container.
- A component part cannot simultaneously be part of other objects.

### Implementation

In Java:

- Aggregation and composition are translated in the same way.

In C++:

- They can be represented differently.

---

# 9. Reflexive Associations

A **reflexive association** is an association involving objects of the **same class**.

It represents relationships between multiple objects belonging to the same class.

Example:

```
Persona -------- Persona
```

Possible interpretation:

```
employee manages employee
```

---

# 10. Inheritance / Generalization

Inheritance, or **generalization**, expresses common behavior between classes.

It allows a class to inherit from another class.

Example:

```
Bicicletta
    ▲
    |
Triciclo
```

In Java:

```
class Triciclo extends Bicicletta
```

### Multiple Inheritance

A class may conceptually inherit from multiple classes.

- Java does **not** allow multiple inheritance of classes.
- Multiple inheritance can produce conflicts when different parent classes provide attributes or services with the same name.

---

# 11. Abstract Classes

An **abstract class** cannot have instances.

It is used to define common characteristics/behavior for subclasses without creating objects of the abstract class itself.

```
AbstractClass
      ▲
      |
   Subclass
```

---

# 12. Interfaces

Interfaces, like abstract classes, **cannot have instances**.

They define a contract that implementing classes must provide.

An interface can be related to classes through an **implementation** relationship.

```
Class -------- implements --------> Interface
```

Interfaces can also be combined with inheritance relationships.

![[Pasted image 20260921121117.png|609]]

---

# 13. Object Diagrams

An **object diagram** represents instances of classes.

While:

- **Class diagram** → describes classes and their structure.
- **Object diagram** → describes concrete objects/instances.

Object diagrams therefore provide a view of the system at the instance level.

![[Pasted image 20260921121052.png]]

---

# 14. Packages

A **package** is a mechanism for structuring a system.

It:

- Defines a **namespace**.
- Allows hierarchical decomposition.
- Represents dependencies between packages.

Packages provide a way of organizing related classes and other UML elements.

	![[Pasted image 20260921121316.png]]

---

# 15. Interaction Diagrams

Interaction diagrams describe the **dynamic behavior** of a group of objects that interact to solve a problem.

They represent scenarios in terms of:

- **Entities** → objects
- **Messages exchanged** → methods

UML proposes:

- **Sequence diagrams**
- **Communication diagrams**

---

# 16. Sequence Diagrams

Sequence diagrams represent interactions between objects in the order in which they occur.

A scenario can initially be represented using entities and messages:

```
Pippo → Selettore Informatica : scegli informatica
Selettore Informatica → Corso : disponibile?
Selettore Informatica → Corso : aggiungi(Pippo)
Selettore Informatica → Corso : conferma registrazione
```

The interaction can then be refined into explicit objects and method calls.

Example:

```
:Pippo
:Selettore Informatica
:Corso

seleziona(informatica)
disponibile()
aggiungi(pippo)
conferma(informatica)
```

Responses may remain implicit or be represented explicitly.

---

# 17. Sequence Diagram Elements

### Lifeline

A **lifeline** is the dashed vertical line associated with an object.

It represents the object's existence during the interaction.

### Activation

A rectangle on a lifeline represents the period during which the object is **active**.

### Object Destruction

A lifeline can terminate when the corresponding object ceases to exist.

### Explicit Response

Responses can be explicitly represented in the sequence diagram.

---

# 18. Types of Messages

Sequence diagrams can represent different types of messages exchanged between objects.

Messages correspond to operations/method invocations between interacting objects.

---

# 19. Interaction Frames

Interaction frames allow parts of an interaction to be structured.

They can represent:

- **Repeated operations**
- **Optional operations**
- **Alternatives**

### Alternative

An alternative interaction represents different possible execution paths depending on conditions.

Conceptually:

```
if condition
    interaction A
else
    interaction B
```

---

# 20. Communication Diagrams

Communication diagrams are interaction diagrams that represent the communication between objects.

They focus on:

- Objects involved in the interaction.
- Messages exchanged between them.
- The relationships through which objects communicate.

![[Pasted image 20260921123414.png|556]]

![[Pasted image 20260921123443.png|565]]

---

# 21. Finite State Machines

State machine diagrams represent the behavior of individual objects of a class.

They describe:

- **Events** to which objects are sensitive.
- **Actions** produced.
- **State transitions**.
- Internal states of objects.
- Possible parallel evolutions.

The syntax is derived from **StateChart**, by David Harel.

---

# 22. States, Events and Transitions

A state represents a condition of an object during its execution.

A transition occurs when an event causes the object to move from one state to another.

### Event + Condition

Conditions can be used when an event alone is not sufficient.

A transition can therefore depend on:

- An **event**
- A **condition/guard**

Conceptually:

```
event [condition]
```

The condition is a Boolean function over object values.

---

# 23. Actions in States

A state can contain actions associated with different moments of its execution.

### Entry Action

Executed when entering the state.

```
entry / action
```

### Do Action

Executed while remaining in the state.

```
do / action
```

### Exit Action

Executed when leaving the state.

```
exit / action
```

### Transition Action

Executed as part of a transition.

```
event [condition] / action
```

---

# 24. Complete State Description

A state can include:

```
StateName

attribute1: type1 = initialValue
attribute2: type2 = initialValue

entry / action
do / action
exit / action

event1 / action
event2 / action
event3 [condition] / action
```

The notation combines:

- State name
- Internal attributes
- Entry action
- Do activity
- Exit action
- Event-triggered transitions
- Conditions
- Transition actions

---

# 25. OR Decomposition

A macro-state can be decomposed using **OR decomposition**.

- Only **one** of the constituent states can be active at a given time.
- More precisely, this is an **XOR** relationship.
- Substates inherit the transitions of their superstates.

```
Macro-state
 ├── State A
 ├── State B
 └── State C
```

Only one of `A`, `B`, or `C` is active at a time.

![[Pasted image 20260921124808.png|533]]

---

# 26. AND Decomposition

AND decomposition is dual to OR decomposition.

- There is one active state for each macro-state.
- It models **concurrent operations and activities**.

Therefore, multiple substates can be active simultaneously.

```
Macro-state
 ├── State A  ← active
 └── State B  ← active
```

![[Pasted image 20260921124750.png|544]]

---

# 27. History

A **history** can be associated with non-leaf states.

When execution leaves a state `S` containing a history:

1. The last state visited inside `S` is saved in the history.
2. When execution returns to `S`, execution resumes from the last saved state.

Conceptually:

```
S
 ├── A
 ├── A1
 ├── A2
 └── B

H = history of S
```

History therefore allows a composite state to remember where execution was before leaving it.

![[Pasted image 20260921124619.png|542]]

---

# 28. Component Diagrams

Component diagrams are useful for **decomposing the system**.

They show components and the interfaces they provide or require.

A component can expose:

- **Functions offered by the component**
- **Interfaces offered**
- **Interfaces required**

They provide a higher-level view of the system's structural decomposition.

![[Pasted image 20260921124847.png]]

Square: component
Circle: offers the interface.
Half circle: uses the interface of another component.

---

# Exam Essentials

### UML Fundamentals

- **Model** = abstract description of a system and its environment.
- Models are abstractions and omit distracting details.
- Different models provide different system perspectives.

### Main Diagram Categories

```
Structure:
- Class
- Object
- Component
- Composite structure
- Package
- Deployment

Behavior:
- Use case
- Activity
- State machine

Interaction:
- Sequence
- Communication
- Timing
- Interaction overview
```

### Use Cases

- Behavioral diagrams.
- Describe system requirements.
- Show **what** functionalities are offered and **who** uses them.
- Do not describe implementation details.

### Class Diagrams

```
Class = Name + Attributes + Methods
```

Attribute:

```
visibility name: type [multiplicity] = default {properties}
```

Method:

```
visibility name(parameters): return type {properties}
```

Visibility:

```
+ public
- private
# protected
~ friendly
```

### Associations

- Relationship between classes.
- Can have a name and roles.
- Association ends behave as implicit attributes.
- Association ends have visibility and multiplicity.

Common multiplicities:

```
1
0..1
1..*
4
6-12
```

### Aggregation vs Composition

```
Aggregation:
part-of relationship

Composition:
strong aggregation
→ parts cannot exist without container
→ creation/destruction within container
→ parts cannot belong to other objects
```

### Inheritance

- Also called **generalization**.
- Expresses common behavior.
- Java does not allow multiple inheritance of classes.
- Multiple inheritance may cause conflicts between inherited attributes/services.

### Abstract Classes vs Interfaces

```
Abstract class → cannot have instances
Interface      → cannot have instances
```

Interfaces define relationships through implementation.

### Interaction Diagrams

- Describe dynamic behavior of interacting objects.
- Main types in the lecture:
    - Sequence diagrams
    - Communication diagrams

### Sequence Diagrams

Know:

```
Lifeline     → object's existence
Activation   → period of activity
Message      → method/interaction
Response     → can be explicit
Frame        → structures interaction
```

Frames can represent:

```
repetition
optional behavior
alternatives
```

### State Machines

Describe:

```
Events
Actions
States
Transitions
```

Transition:

```
event [condition] / action
```

State actions:

```
entry / action
do / action
exit / action
```

### State Decomposition

```
OR/XOR:
→ only one substate active

AND:
→ concurrent substates active
```

### History

- Saves the last visited state of a composite state.
- Returning to the state resumes from the saved state.

### Component Diagrams

- Decompose the system into components.
- Show interfaces offered/required and functions provided.