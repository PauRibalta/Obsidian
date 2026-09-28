## 1. Java Overview

- Main characteristics:
    
    - **Object-oriented**
    - Platform-independent through **bytecode + Java Virtual Machine (JVM)**
    - Supports distributed applications
    - Secure execution through a **sandbox**

### Java Program Structure

A Java program is organized as a set of **classes**.

A class:

- Defines a type.
- Contains **attributes** (variables) and **methods** (functions).
- Attributes characterize the state of individual objects.
- Methods manipulate objects.
- The main program is represented by the special `main` method.

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello world!");
    }
}
```

---

# 2. Classes and Objects

## Classes as Abstractions

A class is a **user-defined type** specifying the operations available on that type.

```java
Data d;
```

`d` is a variable of type `Data`, and can use the operations defined by `Data`.

A class therefore defines an **Abstract Data Type (ADT)**.

## Objects

Objects of the same class:

- Have the same structure.
    
- Have the same number and types of attributes.
    
- Can have different attribute values.
    

The **state** of an object is the current value of its attributes.

### Accessing attributes and methods

Use **dot notation**:

```java
x = d.leggiGiorno();
```

Calling a method on an object can also be described as **sending a message** to that object.

## Object State

Methods can change object state.

Inside a method, attributes and methods of the current object can be accessed directly:

```java
public void giornoDopo() {
    giorno++;

    if (giorno > 31) {
        giorno = 1;
        mese++;
    }

    if (mese > 12) {
        mese = 1;
        anno++;
    }
}
```

Some objects are **immutable**: their state cannot be modified after creation.

Example: `String`.

---

# 3. Visibility: `public` and `private`

- `public` members can be accessed from outside the class.
    
- `private` members can only be accessed inside the class.
    

```java
public int leggiGiorno() { ... }
```

allows external access, while:

```java
private int giorno;
```

does not.

This implements **encapsulation / information hiding**.

---

# 4. Primitive Types

|Type|Size|
|---|--:|
|`byte`|8 bit|
|`short`|16 bit|
|`int`|32 bit|
|`long`|64 bit|
|`float`|32 bit|
|`double`|64 bit|
|`char`|16 bit, Unicode|
|`boolean`|`true` / `false`|

Example:

```java
int a, b = 3, c;
char c = 'h';
boolean trovato = false;
```

## Default Values for Fields

```text
byte, short, int → 0
long              → 0L
float             → 0.0f
double            → 0.0d
char              → \u0000
boolean           → false
reference types   → null
```

---

# 5. Reference Types

Primitive variables contain their value directly.

Reference variables contain a **reference to an object**.

Reference types include:

- Classes
    
- Interfaces
    
- Arrays
    
- Enumerations
    

```java
Data d;
```

does **not** create a `Data` object.

It only creates a reference variable.

Initially:

```text
d = null
```

The actual object is allocated separately on the heap.

## Stack vs Heap

- Local variables/references are allocated in the **stack activation record**.
    
- Objects referenced by them are allocated on the **heap**.
    
- Local variables disappear when the method terminates.
    
- Objects may remain alive after the method terminates if they are still referenced.
    

---

# 6. Object Creation: `new`

Objects are dynamically created with `new`.

```java
Data d = new Data();
```

`new`:

1. Creates a new object.
    
2. Returns a reference to it.
    
3. Stores that reference in `d`.
    
4. Invokes the constructor `Data()`.
    

### Lost References

```java
data = new Data();
data = new Data();
```

After the second assignment, the first object has no accessible reference.

It can eventually be destroyed by the **garbage collector**.

## Garbage Collection

Java does not require explicit object deallocation.

The **garbage collector** automatically frees memory when objects are no longer reachable.

---

# 7. Constructors

A constructor:

- Has the same name as the class.
    
- Has no return type.
    
- Is invoked when an object is created.
    
- Allocates/initializes the object.
    

```java
public Data(int g, int m, int a) {
    giorno = g;
    mese = m;
    anno = a;
}
```

## Default Constructor

If no constructor is explicitly defined, the compiler provides a parameterless default constructor.

It initializes:

- Numeric values → `0`
    
- Boolean values → `false`
    
- References → `null`
    

If the programmer defines any constructor, the compiler **does not provide the default constructor**.

Therefore:

```java
class C {
    C(int x) { ... }
}

C c = new C();   // ERROR
```

## Constructor Overloading

A class may define several constructors with different parameter lists:

```java
public C(int a) { ... }

public C(int a, int b) { ... }
```

## Calling Another Constructor

Use:

```java
this(parameters);
```

It must be the **first instruction** of the constructor.

```java
public Data(int g, int m) {
    this(g, m, 1900);
}
```

---

# 8. Sharing / Aliasing

Assigning reference variables copies the **reference**, not the object.

```java
Data d1 = new Data(20, 3, 2007);
Data d2 = d1;
```

Now `d1` and `d2` refer to the **same object**.

This is called **sharing / aliasing**.

If the object is mutable:

```java
d2.giornoDopo();
```

the modification is visible through `d1` as well.

---

# 9. Arrays

For a type `T`:

```java
T[]
```

Examples:

```java
int[] a;
float[] f;
Persona[] p;
int[][] matrix;
```

## Array Creation

Declaration alone does not allocate the elements:

```java
int[] a;
```

Dynamic allocation:

```java
int[] a = new int[10];
```

The array has 10 elements.

Initialization can also be explicit:

```java
int[] a = {1, 2, 3};
```

## Arrays of Objects

```java
Person[] person = new Person[20];
```

This creates:

- An array object.
    
- Space for 20 **references**.
    

It does **not** create 20 `Person` objects.

An object must be created separately:

```java
person[0] = new Person();
```

## Generalized `for`

```java
for (Person p : people) {
    ...
}
```

The variable represents the **element**, not its index.

---

# 10. Parameter Passing

When calling a method:

1. Actual parameters are evaluated.
    
2. An activation record is created.
    
3. Parameters and local variables are stored.
    
4. The method body executes.
    

According to the course material:

- Primitive types are passed **by copy**.
    
- Reference types allow the called method to operate on the referenced object.
    

Example:

```java
public void copiaIn(Data d) {
    d.giorno = giorno;
    d.mese = mese;
    d.anno = anno;
}
```

The reference passed to the method allows the method to modify the referenced object.

---

# 11. `this`

`this` is a pseudo-variable containing a reference to the **current object**.

Useful when a local variable/parameter hides an attribute:

```java
public void trasforma(String marca, String modello) {
    this.marca = marca;
    this.modello = modello;
}
```

It can also explicitly identify the current object's attributes:

```java
this.giorno++;
```

### Returning `this`

A method can return the current object:

```java
public InsiemeDiInteri inserisci(int i) {
    // modify this
    return this;
}
```

This allows method chaining:

```java
z = x.inserisci(2).unione(y);
```

---

# 12. `static`: Class Attributes and Methods

A `static` member belongs to the **class**, not to individual objects.

```java
static <definition>
```

### Static Attribute

Shared by all instances:

```java
static Screen screen;
```

Access:

```java
Shape.screen
```

### Static Method

Can be invoked without an object:

```java
Shape.setScreen(new Screen());
```

A `static` method can access only:

- Static attributes
    
- Static methods
    

A normal instance method can access static members.

---

# 13. Constants: `final`

A constant attribute can be declared using `final`:

```java
final static int BLU = 0;
```

Its value cannot be changed.

```java
a.BLU = 128;    // ERROR
```

---

# 14. Method Overloading

A class may contain multiple methods with the same name if their **parameter lists differ**.

Different:

- Number of parameters
    
- Types of parameters
    
- Parameter positions/types
    

The return type alone is **not sufficient**.

```java
public int max(int a, int b) { ... }

public double max(double a, double b) { ... }

public int max(int a, int b, int c) { ... }
```

The compiler selects the appropriate method from the method signature.

---

# 15. Reference Equality vs Object Equality

## `==`

For reference variables, `==` compares **references / identity**, not object state.

```java
Data d1 = new Data(1, 12, 2001);
Data d2 = new Data(1, 12, 2001);

d1 == d2    // false
```

The objects have equal state, but are different objects.

After:

```java
d1 = d2;
```

they are aliases, so:

```java
d1 == d2    // true
```

## `equals()`

`equals()` checks object **equivalence**, according to the class definition.

For example, two `String` objects are equal if they contain the same character sequence.

```java
String b = new String("Ciao");
String c = new String("Ciao");

b.equals(c)    // true
```

**Exam distinction:**

```text
==       → reference identity
equals() → object equality/equivalence
```

---

# 16. `String`

`String` objects are **immutable**.

Their contents cannot be modified; operations that appear to modify a string create another string.

Useful methods:

```java
length()
charAt(index)
substring(beginIndex)
```

Indexing starts at `0`.

Concatenation:

```java
String s = "Hello " + name;
```

Reference assignment:

```java
String d = b;
```

does not copy the object; `d` and `b` become aliases.

---

# 17. Enumerations

Enumerations define a type with a restricted number of values.

```java
enum Size {
    SMALL, MEDIUM, LARGE, X_LARGE
}
```

Usage:

```java
Size s = Size.MEDIUM;
```

An enum is a real class with a fixed set of instances.

No additional instances can be created.

Enum values can be compared with:

```java
s == Size.MEDIUM
```

### Enum Methods

```java
name()
ordinal()
toString()
compareTo(...)
values()
valueOf(...)
```

Example:

```java
for (Color c : Color.values()) {
    ...
}
```

Enums can have:

- Attributes
    
- Constructors
    
- Methods
    

---

# 18. Wrapper Types

Java provides reference types corresponding to primitive types:

```text
Integer
Character
Float
Long
Short
Double
```

Example:

```java
Integer i = new Integer(5);
```

Automatic **boxing**:

```java
int y = 3;
Integer i = y;
```

Automatic **unboxing**:

```java
int y = i;
```

Wrapper objects such as `Integer` are immutable.

---

# 19. Chained Dot Notation

The `.` operator is **left-associative**.

```java
b.substring(1).substring(2);
```

means:

```java
(b.substring(1)).substring(2);
```

Similarly:

```java
System.out.println();
```

means:

```java
(System.out).println();
```

---

# 20. Inheritance

Inheritance defines a **subclass-of** relationship.

```java
class B extends A {
    ...
}
```

- `A` = superclass / base class / parent
    
- `B` = subclass / derived class / child
    

A subclass inherits the implementation of its superclass and can:

- Add attributes
    
- Add methods
    
- Override methods
    

## Overriding

A subclass can redefine a superclass method.

The method signature must remain compatible.

```java
class AutomobileElettrica extends Automobile {
    public void accendi() {
        ...
    }
}
```

This is **overriding**, not overloading.

## `super`

Used to access superclass methods:

```java
super.accendi();
```

---

# 21. Constructors and Inheritance

Constructors are **not inherited**.

A subclass constructor can invoke a superclass constructor using:

```java
super(parameters);
```

`super(...)` must be the **first instruction**.

```java
public AutomobileElettrica(String modello) {
    super(modello);
    batterieCariche = false;
}
```

If no superclass constructor is explicitly invoked, Java automatically tries to invoke the superclass **default constructor**.

If it does not exist, compilation fails.

---

# 22. `Object`

If no other superclass is specified, a Java class extends `Object`.

`Object` provides methods including:

```java
equals(Object)
toString()
hashCode()
```

---

# 23. Packages

A **package** groups classes and defines a namespace and visibility rules.

```java
package myTools.text;
```

A class can be referenced using:

```text
package.Class
```

Packages allow classes with the same name to coexist in different packages.

## Compilation Unit

A Java source file can contain one or more class/interface declarations.

- At most one `public` class.
    
- If there is a public class, its name must match the file name.
    
- At most one `main` method is relevant as the program entry point.
    
- A package can be specified for the compilation unit.
    

---

# 24. Visibility of Classes and Members

## Classes

### `public`

- Visible outside the package.
    
- Can be imported.
    
- At most one public class per file.
    

### Friendly / package-private

- Visible within the same package.
    
- Can coexist with other classes in the same file.
    

## Attributes and Methods

|Modifier|Visibility|
|---|---|
|`public`|Everywhere|
|`private`|Only inside the declaring class|
|`protected`|Same package + subclasses|
|friendly|Same package|

`private` members are **not directly accessible from subclasses**.

---

# 25. Information Hiding

A public member is effectively a **promise** to users of the class.

Therefore:

- Keep implementation details private.
    
- Prefer private attributes.
    
- Use public methods to access/manipulate state.
    
- Use package-private/friendly members only when classes inside the same package need privileged access.
    

General principle:

```text
Expose the interface, hide the implementation.
```

---

# 26. Polymorphism

Inheritance allows a variable of type `T` to refer to an object whose type is `T` or a subtype of `T`.

```java
Automobile myCar = new AutomobileElettrica();
```

Two types must be distinguished:

- **Static type** → type declared for the variable; known at compile time.
    
- **Dynamic type** → actual type of the referenced object; determined at runtime.
    

Example:

```java
Automobile myCar = new AutomobileElettrica();
```

```text
Static type  = Automobile
Dynamic type = AutomobileElettrica
```

## Polymorphic Assignment

A subtype can be assigned to a variable of a supertype:

```java
Automobile a = new AutomobileElettrica();
```

The opposite direction is not automatically allowed.

---

# 27. Method Calls and Dynamic Binding

For:

```java
x.f(...)
```

the implementation selected for `f` depends on the **dynamic type of `x`**.

Therefore Java uses **dynamic binding / dynamic dispatch** for overridden methods.

```java
Automobile a = new AutomobileElettrica();
a.accendi();
```

Even though the static type is `Automobile`, the overridden implementation in `AutomobileElettrica` is selected at runtime.

### Important distinction

```text
Overloading  → resolved statically / compile time
Overriding   → dynamic binding / runtime
```

---

# 28. Overloading vs Overriding

**Overloading**

- Same method name.
    
- Different parameter list/signature.
    
- Resolved statically.
    

**Overriding**

- Subclass redefines inherited method.
    
- Compatible signature.
    
- Dynamic binding applies.
    

Example:

```java
class Punto2D {
    float distanza(Punto2D p) { ... }
}

class Punto3D extends Punto2D {
    float distanza(Punto3D p) { ... }
}
```

This is **overloading**, not overriding, because the parameter type differs.

---

# 29. Abstract Classes and Methods

An **abstract method** has no implementation.

```java
abstract void show();
```

An **abstract class** can contain:

- Abstract methods
    
- Implemented methods
    
- Attributes
    

An abstract class **cannot be instantiated**.

```java
abstract class Shape {
    abstract void show();
}
```

```java
Shape s = new Shape();   // ERROR
Circle c = new Circle(); // OK
Shape s = new Circle();  // OK
```

Abstract classes provide higher-level abstractions.

---

# 30. `final` Classes and Methods

A `final` class cannot be subclassed:

```java
final class C { ... }
```

```java
class C1 extends C { ... }  // ERROR
```

A `final` method cannot be overridden:

```java
class C {
    final void f() { ... }
}
```

---

# 31. Interfaces

Java uses interfaces to provide a form of **multiple implementation inheritance** while keeping class inheritance single.

An interface traditionally contains:

- Constant attributes
    
- Public abstract methods
    

```java
interface Scalable {
    int SMALL = 0;
    int MEDIUM = 1;
    int BIG = 2;

    void setScale(int size);
}
```

Interface attributes are implicitly:

```text
public static final
```

## Interface Inheritance

An interface can extend multiple interfaces:

```java
interface C extends A, B {
    ...
}
```

## Implementing Interfaces

A class can implement multiple interfaces but extend at most one class:

```java
class C extends A implements I1, I2 {
    ...
}
```

If a non-abstract class implements an interface, it must provide implementations for all required methods.

### Abstract Class vs Interface

```text
Abstract class:
- Can contain implemented methods
- Can contain abstract methods
- Can contain state/attributes

Interface:
- Defines an implementation contract
- Traditionally contains abstract methods and constants

Concrete class:
- All required methods implemented
```

---

# 32. Type Conversion

## Primitive Type Promotions

```text
byte  → short → int → long → float → double
char  → int → long → float → double
```

Other promotions include:

```text
short → int, long, float, double
int   → long, float, double
long  → float, double
float → double
```

## Casting

Explicit conversion:

```java
(tipo) expression
```

Example:

```java
double x = 3.5;
int y = (int)x;
```

Casting may cause information loss.

---

# 33. Reference Casting

A reference can be explicitly cast from a superclass type to a subtype if the object's **dynamic type** is compatible.

```java
Animal a = ...;
Gatto g = (Gatto)a;
```

This is valid only if `a` actually refers dynamically to a `Gatto`.

Otherwise a runtime error occurs.

## `instanceof`

Use `instanceof` to test the dynamic type before casting:

```java
if (a instanceof Gatto) {
    Gatto g = (Gatto)a;
}
```

---

# 34. Exceptions

Exceptions provide a mechanism for handling **exceptional situations** where a method cannot produce a meaningful result or cannot terminate normally.

Examples:

- File does not exist.
    
- Invalid input.
    
- Illegal operation.
    
- Arithmetic error.
    

An exception is a special object with a type and potentially additional information.

## `try` / `catch`

```java
try {
    x = x / y;
}
catch (DivisionByZeroException e) {
    // handle exception
}
```

If the exception is handled, execution continues after the `try/catch` construct.

## Multiple `catch`

```java
try {
    ...
}
catch (FileInesistenteException e) {
    ...
}
catch (FileDanneggiatoException e) {
    ...
}
```

A catch block can handle exceptions whose type is the catch type or a subtype.

---

# 35. Exception Propagation

If an exception is not handled in the current block:

1. Search enclosing `try/catch` blocks.
    
2. If none handles it, propagate to the caller.
    
3. Continue up the call chain.
    
4. If no handler is found, the program terminates.
    

The exception therefore propagates through the call stack until a suitable handler is found.

---

# 36. `finally`

A `finally` block is executed regardless of whether:

- No exception occurs.
    
- An exception occurs and is handled by a `catch`.
    
- An exception occurs and is not handled by a local `catch`.
    

Structure:

```java
try {
    ...
}
catch (IOException e) {
    ...
}
finally {
    ...
}
```

Useful for operations that must be performed regardless of the result.

---

# 37. `throws`

A method can declare exceptions in its interface:

```java
public int read() throws IOException
```

Multiple exceptions:

```java
public int search(int[] a, int x)
    throws NullPointerException, NotFoundException
```

`throws` indicates that the method may terminate by propagating those exceptions.

---

# 38. `throw`

`throw` explicitly raises an exception:

```java
if (n < 0)
    throw new NegativeException();
```

Example:

```java
public int fact(int n) throws NegativeException {
    if (n < 0)
        throw new NegativeException();

    if (n == 0 || n == 1)
        return 1;

    return n * fact(n - 1);
}
```

---

# 39. Checked vs Unchecked Exceptions

Exception hierarchy:

```text
Throwable
├── Error
└── Exception
    └── RuntimeException
```

### Checked Exceptions

Subtypes of `Exception` (excluding `RuntimeException`).

They must be:

- Declared with `throws`, or
    
- Handled with `try/catch`.
    

Otherwise compilation fails.

### Unchecked Exceptions

Subtypes of `RuntimeException`.

They:

- Do not have to be declared.
    
- Do not have to be explicitly caught.
    
- Can propagate automatically.
    

Examples include runtime errors such as invalid array access.

### Key distinction

```text
Checked:
    must handle OR declare

Unchecked:
    no compile-time requirement to handle/declare
```

---

# 40. User-Defined Exceptions

A custom exception can extend `Exception` or `RuntimeException`.

```java
class DataIllegaleException extends Exception {
}
```

It can contain its own:

- Attributes
    
- Methods
    
- Constructors
    

Convention: exception class names normally end in:

```text
Exception
```

Example:

```java
throw new NewKindOfException("problema!!!");
```

---

# 41. Collections

Java provides a hierarchy of standard collection types.

Important collection abstractions include:

- `List`
    
- `Deque`
    
- `Map`
    
- Other collection types
    

The choice depends on the operations required by the problem.

---

# 42. `List`

A `List` represents a dynamic sequence of elements.

Common implementations:

- `ArrayList`
    
- `LinkedList`
    

Lists are **generic**:

```java
List<Person> team = new ArrayList<Person>();
```

## Main Operations

```java
add(object)
add(index, object)
remove(index)
get(index)
set(index, object)
size()
```

Indexes start at `0`.

Example:

```java
team.add(new Person("Bob"));
team.add(1, new Person("Sue"));

team.remove(0);

team.get(1);
team.set(0, new Person("Mary"));
```

`set()` replaces an existing element; it does not create a new position.

### ArrayList Cost

`add`/`remove` can be expensive when they require shifting array elements.

---

# 43. Generics

Generic types are parameterized by a type:

```java
List<String>
List<Integer>
Collection<Person>
```

A key rule:

```text
If ClassB extends ClassA:

Generic<ClassB> is NOT a subtype of Generic<ClassA>
```

Example:

```java
List<String> ls = new ArrayList<String>();
List<Object> lo = ls;   // ERROR
```

Otherwise an `Object` could be inserted into a list that is supposed to contain only `String`.

---

# 44. Generic Methods

Generic methods can introduce their own type parameter:

```java
static <T> void fromArrayToCollection(
    T[] a,
    Collection<T> c) {

    for (T o : a)
        c.add(o);
}
```

The compiler can often **infer** the actual type parameter.

Example:

```java
String[] sa = new String[100];

Collection<String> cs = new ArrayList<String>();
fromArrayToCollection(sa, cs);
```

`T` is inferred as `String`.

It can also infer a supertype when required by the invocation.

---

# 45. Iterators

A collection's internal organization should be hidden from the client.

An **iterator** provides an abstraction for traversing a collection without knowing its internal representation.

Main operations:

```java
hasNext()
next()
remove()
```

Java interface:

```java
public interface Iterator<E> {
    boolean hasNext();
    E next() throws NoSuchElementException;
    void remove();
}
```

### Typical Usage

```java
Iterator<Integer> itr = collection.iterator();

while (itr.hasNext()) {
    Integer x = itr.next();
}
```

The iterator separates:

```text
traversal operation
        from
underlying data structure
```

---

# 46. `Iterable`

`Iterable` standardizes access to iterators.

A class implementing `Iterable` provides:

```java
iterator()
```

This allows the collection to be used with the generalized `for` loop:

```java
for (Person p : collection) {
    ...
}
```

The implementation must provide an efficient iterator.

---

# 47. Iterator `remove()`

`remove()` is optional.

If unsupported:

```text
UnsupportedOperationException
```

The contract is that `remove()` deletes the last element returned by `next()`.

Restrictions:

- `next()` must have been called.
    
- `remove()` can only be called once per `next()`.
    
- The collection must not otherwise be modified during the iteration.
    

Invalid usage can cause:

```text
IllegalStateException
```

---

# 48. Input / Output

## Input with `Scanner`

Create a scanner connected to standard input:

```java
Scanner in = new Scanner(System.in);
```

Useful methods:

```java
nextLine()      // next line
next()          // next token/string
nextInt()       // next int
nextDouble()    // next double
nextFloat()
nextBoolean()

hasNext()
hasNextInt()
```

## Output

```java
System.out.print(...)
System.out.println(...)
System.out.printf(...)
```

`printf` uses C-like formatting conventions.

Example:

```java
Scanner in = new Scanner(System.in);

String nome = in.nextLine();
int eta = in.nextInt();

System.out.println(
    "Ciao " + nome + " tra un anno avrai " + (eta + 1) + " anni"
);
```

---

# 49. Annotations

Annotations provide metadata to the compiler/tools.

Important annotations from the course:

### `@Deprecated`

Marks an element as deprecated and not recommended for continued use.

### `@Override`

Informs the compiler that a method is intended to override a superclass method.

Useful for detecting overriding mistakes.

```java
@Override
public void accendi() {
    ...
}
```

### `@SuppressWarnings`

Requests that the compiler suppress specific warnings.

```java
@SuppressWarnings({"unchecked", "deprecation"})
```

---

# Exam Essentials

## Java Basics

```text
Java program → classes
Class → attributes + methods
Object → instance of a class
State → values of object attributes
```

## References

```text
Primitive variable → contains value
Reference variable → contains reference to object
new → creates object
null → no referenced object
```

## Memory

```text
Local variables/references → stack
Objects → heap
Garbage collector → automatically frees unreachable objects
```

## Constructors

```text
Constructor:
- same name as class
- no return type
- invoked by new
- initializes object

this(...) → calls another constructor; must be first
super(...) → calls superclass constructor; must be first
```

## Equality

```text
==        → reference identity
equals()  → object equivalence
```

## Static / Final

```text
static → belongs to class
final  → cannot be changed / overridden / extended, depending on usage
```

## Inheritance

```text
extends → subclass inheritance
super.method() → call superclass method
super(...) → call superclass constructor
```

## Overloading vs Overriding

```text
Overloading:
- same name
- different parameters
- compile-time resolution

Overriding:
- subclass redefines inherited method
- dynamic binding
- runtime method selection
```

## Polymorphism

```text
Static type  → declared type
Dynamic type → actual object type

Superclass reference → can refer to subclass object
```

## Abstract / Interface

```text
abstract class → cannot instantiate
abstract method → no implementation

interface → implementation contract
class → can implement multiple interfaces
class → can extend at most one class
```

## Casting

```java
(Type) expression
```

For reference casting, the dynamic object must be compatible with the target type.

```java
instanceof
```

can be used to check before casting.

## Exceptions

```text
try       → code that may fail
catch     → handles exception
finally   → executed regardless
throw     → explicitly raises exception
throws    → declares possible propagated exceptions
```

```text
Checked exception:
    handle OR declare

Unchecked exception:
    no compile-time requirement
```

## Collections

```text
List<T> → dynamic ordered collection
ArrayList<T>
LinkedList<T>

Iterator<T>:
    hasNext()
    next()
    remove()

Iterable:
    iterator()
    enables generalized for
```

## Critical distinctions to remember

```text
class vs object
primitive vs reference
stack vs heap
== vs equals()
overloading vs overriding
static type vs dynamic type
abstract class vs interface
checked vs unchecked exception
List vs Iterator
```