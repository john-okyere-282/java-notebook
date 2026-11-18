# Java Glossary

Plain-English definitions for terms you will encounter throughout this notebook.
Terms are listed alphabetically.

---

### Abstract class
A class declared with the `abstract` keyword that cannot be instantiated directly. It may contain both abstract methods (no body, subclasses must implement) and concrete methods (with a body). Use it when you want to share code among related classes while enforcing that certain methods are overridden.
```java
abstract class Shape {
    abstract double area();           // subclasses must implement
    void describe() {                 // shared concrete method
        System.out.println("I am a shape with area: " + area());
    }
}
```

### Abstraction
An OOP principle that hides implementation details and exposes only what is necessary. You interact with a `List` through its interface methods without knowing whether it is an `ArrayList` or `LinkedList` underneath.

### Access modifier
A keyword that controls the visibility of a class, method, or field. Java has four: `public` (everywhere), `protected` (same package + subclasses), package-private (no keyword, same package only), and `private` (same class only).

### Algorithm
A step-by-step procedure for solving a problem. In code, an algorithm is typically a method or set of methods that transforms input into output in a defined number of steps.

### Annotation
Metadata attached to code using the `@` symbol. Annotations do not change program logic directly but can be read by the compiler, build tools, or frameworks at runtime. Examples: `@Override`, `@Test`, `@SpringBootTest`.
```java
@Override
public String toString() { return "MyClass"; }
```

### API
Application Programming Interface. A defined set of classes, methods, and contracts that one piece of software exposes so another can use it without knowing its internals. The Java standard library is itself a large API.

### Argument
The actual value passed to a method when calling it. Distinguished from a *parameter*, which is the variable declared in the method signature.
```java
greet("Alice");   // "Alice" is the argument
void greet(String name) { ... }  // name is the parameter
```

### Array
A fixed-size, ordered collection of elements of the same type stored in contiguous memory. Array size cannot change after creation.
```java
int[] scores = new int[5];
String[] days = {"Mon", "Tue", "Wed", "Thu", "Fri"};
System.out.println(days[0]); // Mon
```

### ArrayList
A resizable array implementation of the `List` interface in `java.util`. It grows automatically as elements are added. Backed by a plain array internally; random access is O(1) but insertion in the middle is O(n).
```java
List<String> names = new ArrayList<>();
names.add("Alice");
names.add("Bob");
```

### Assertion
A statement that verifies an expected condition. In testing (JUnit 5), assertions check that actual values match expected values.
```java
assertEquals(42, calculator.add(20, 22));
assertTrue(list.isEmpty());
```

### Autoboxing
The automatic conversion Java performs when a primitive type (e.g. `int`) is used where its wrapper class (`Integer`) is expected.
```java
List<Integer> list = new ArrayList<>();
list.add(42);  // int 42 is autoboxed to Integer(42)
```

### Big O notation
A mathematical notation describing how the time or space requirements of an algorithm grow as input size n increases. Common classes: O(1) constant, O(log n) logarithmic, O(n) linear, O(n log n) linearithmic, O(n²) quadratic.

### Bytecode
The intermediate representation produced when you compile Java source code with `javac`. Bytecode is stored in `.class` files and is not machine code — it is executed by the JVM, which translates it to native instructions at runtime.

### Callable
A functional interface in `java.util.concurrent` similar to `Runnable` but whose `call()` method can return a result and throw checked exceptions. Used with `ExecutorService.submit()`.
```java
Callable<Integer> task = () -> 42;
Future<Integer> result = executor.submit(task);
```

### Class
The blueprint or template for creating objects. A class defines fields (state) and methods (behaviour). Every Java program consists of at least one class.
```java
public class Car {
    String model;
    int year;
    void start() { System.out.println(model + " started"); }
}
```

### Class loader
Part of the JVM responsible for finding and loading `.class` files into memory at runtime. Java uses a delegation model with three built-in class loaders: Bootstrap, Platform (Extension), and Application.

### Classpath
The list of directories and JAR files the JVM searches when looking for compiled `.class` files. Set via the `-cp` flag, the `CLASSPATH` environment variable, or build tool configuration.

### Collection
Any object that groups multiple elements into a single unit. The Java Collections Framework (`java.util`) provides interfaces (`List`, `Set`, `Map`, `Queue`) and implementations (`ArrayList`, `HashSet`, `HashMap`, etc.).

### Compiler
A program that translates source code into another form. `javac` is the Java compiler; it converts `.java` source files into `.class` bytecode files.

### Composition
A design approach where a class contains references to other objects to build complex behaviour, rather than inheriting it. Preferred over inheritance when the relationship is "has-a" rather than "is-a".
```java
class Engine { void start() { ... } }
class Car {
    private Engine engine = new Engine();  // Car HAS-A Engine
    void drive() { engine.start(); }
}
```

### Concurrency
The ability of a program to execute multiple tasks in overlapping time periods, either through true parallelism (multiple CPU cores) or time-sliced threading on a single core.

### Constructor
A special method called when an object is created with `new`. It has the same name as the class and no return type. Its job is to initialise the object's fields.
```java
public class Person {
    String name;
    Person(String name) { this.name = name; }
}
Person p = new Person("Alice");
```

### Data structure
A way of organising and storing data to enable efficient access and modification. Arrays, linked lists, stacks, queues, trees, and hash maps are common data structures.

### Deadlock
A situation in concurrent programming where two or more threads are blocked forever, each waiting for a resource the other holds. Prevented by consistent lock ordering and timeouts.

### Dependency injection
A design pattern where an object receives its dependencies from an external source rather than creating them itself. Spring Boot uses it extensively via `@Autowired` or constructor injection.
```java
@Service
public class OrderService {
    private final PaymentService paymentService;
    // dependency injected via constructor
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

### Design pattern
A reusable, named solution to a commonly occurring problem in software design. Examples: Singleton, Factory, Builder, Observer, Strategy, Repository.

### DRY
Don't Repeat Yourself. The principle that every piece of knowledge should have a single, authoritative representation in the codebase. Duplication forces you to change the same logic in multiple places.

### Encapsulation
An OOP principle that bundles data (fields) and the methods that operate on that data into a single unit (class), and restricts direct access to the data using access modifiers. Protects object state from unintended modification.

### Enum
A special class type that defines a fixed set of named constants. Enums in Java are type-safe and can have fields and methods.
```java
enum Day { MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY }
Day today = Day.MONDAY;
```

### Exception
An event that disrupts normal program flow, represented as an object that extends `Throwable`. Checked exceptions (e.g. `IOException`) must be declared or caught. Unchecked exceptions (e.g. `NullPointerException`) extend `RuntimeException` and do not need to be declared.

### ExecutorService
An interface in `java.util.concurrent` that manages a pool of threads. You submit tasks (`Runnable` or `Callable`) and the service handles thread lifecycle.
```java
ExecutorService exec = Executors.newFixedThreadPool(4);
exec.submit(() -> System.out.println("Task running"));
exec.shutdown();
```

### Functional interface
An interface with exactly one abstract method. Functional interfaces can be implemented with a lambda expression. Annotate with `@FunctionalInterface` to let the compiler enforce this constraint.
```java
@FunctionalInterface
interface Transformer { String transform(String input); }
Transformer upper = s -> s.toUpperCase();
```

### Garbage collection
Automatic memory management performed by the JVM. The garbage collector identifies objects that are no longer reachable from any live reference and reclaims their memory. You do not call `free()` in Java.

### Generics
A Java feature that lets you write classes, interfaces, and methods parameterised by type. Generics enable type safety at compile time without casting.
```java
List<String> names = new ArrayList<>();  // only Strings allowed
names.add("Alice");
String s = names.get(0);  // no cast needed
```

### HashMap
A `Map` implementation that stores key-value pairs using a hash table. Provides O(1) average-time for `get`, `put`, and `remove`. Does not guarantee insertion order.
```java
Map<String, Integer> scores = new HashMap<>();
scores.put("Alice", 95);
int s = scores.get("Alice");  // 95
```

### Heap
The region of JVM memory where all objects are allocated. The garbage collector manages the heap. Distinct from the call stack, which holds local variables and method call frames.

### IDE
Integrated Development Environment. A software tool combining a code editor, compiler, debugger, and other features. IntelliJ IDEA and Eclipse are the most common Java IDEs.

### Immutability
A property of an object whose state cannot be changed after creation. `String` is immutable. Immutable objects are thread-safe and easier to reason about.
```java
String s = "hello";
s.toUpperCase();         // creates a new String; s is still "hello"
s = s.toUpperCase();     // now s points to the new String "HELLO"
```

### Import
A declaration at the top of a source file that makes types from other packages available by their simple name.
```java
import java.util.ArrayList;
import java.util.List;
```

### Inheritance
An OOP mechanism where one class (subclass) derives fields and methods from another (superclass) using `extends`. Represents an "is-a" relationship.
```java
class Animal { void breathe() { ... } }
class Dog extends Animal { void bark() { ... } }
// Dog IS-A Animal
```

### Instance
A specific object created from a class. When you write `new Car("Toyota", 2024)`, you create an instance of the `Car` class.

### Interface
A contract that specifies method signatures a class must implement. Declared with the `interface` keyword. A class can implement multiple interfaces. Since Java 8, interfaces can have `default` and `static` methods.
```java
interface Printable { void print(); }
class Report implements Printable {
    public void print() { System.out.println("Printing report"); }
}
```

### Iterator
An object that provides sequential access to elements in a collection without exposing its internal structure. The `Iterator<E>` interface has `hasNext()` and `next()` methods.
```java
Iterator<String> it = list.iterator();
while (it.hasNext()) { System.out.println(it.next()); }
```

### Java
A general-purpose, class-based, object-oriented programming language designed by James Gosling at Sun Microsystems and released in 1995. Java programs compile to bytecode that runs on any JVM, making them platform-independent ("Write Once, Run Anywhere").

### JDBC
Java Database Connectivity. A standard API (`java.sql`) for connecting Java programs to relational databases. You use it to execute SQL queries and process result sets.
```java
Connection conn = DriverManager.getConnection(url, user, pass);
PreparedStatement stmt = conn.prepareStatement("SELECT * FROM employees WHERE id = ?");
stmt.setInt(1, 42);
ResultSet rs = stmt.executeQuery();
```

### JDK
Java Development Kit. The full package a developer needs to write and compile Java programs. Contains the JRE (JVM + libraries) plus the compiler (`javac`), debugger (`jdb`), and other tools.

### JRE
Java Runtime Environment. Contains the JVM and the Java standard libraries. Sufficient to run compiled Java programs; does not include the compiler. (The standalone JRE distribution was discontinued from Java 11; the JDK now covers both roles.)

### JVM
Java Virtual Machine. The runtime engine that executes Java bytecode. Each operating system has its own JVM implementation, which is why Java programs are platform-independent — the same `.class` file runs on Windows, macOS, and Linux.

### KISS
Keep It Simple, Stupid. A design principle favouring simple, clear solutions over clever or complex ones. Simpler code is easier to read, test, and maintain.

### Lambda
A concise way to express an anonymous function (an implementation of a functional interface) without declaring a full class.
```java
// Before lambdas
Comparator<String> c = new Comparator<String>() {
    public int compare(String a, String b) { return a.compareTo(b); }
};
// With a lambda
Comparator<String> c = (a, b) -> a.compareTo(b);
```

### Maven
A build automation and dependency management tool for Java projects. `pom.xml` describes the project structure, dependencies, and plugins. Running `mvn package` compiles the code and packages it into a JAR.

### Method
A named block of code inside a class that performs a specific task. Methods have a return type, a name, and optionally parameters.
```java
public int add(int a, int b) { return a + b; }
```

### Method overloading
Defining multiple methods in the same class with the same name but different parameter lists (number, type, or order of parameters). Java selects the correct version at compile time.
```java
int area(int side) { return side * side; }
double area(double width, double height) { return width * height; }
```

### Method overriding
Providing a new implementation of a method in a subclass that was already defined in a superclass. The method signature must match exactly.
```java
class Animal { String sound() { return "..."; } }
class Cat extends Animal {
    @Override String sound() { return "Meow"; }
}
```

### Mock
A test double that simulates a real object's behaviour so you can test a class in isolation. Mockito is the standard Java mocking library.
```java
UserRepository repo = Mockito.mock(UserRepository.class);
Mockito.when(repo.findById(1L)).thenReturn(Optional.of(new User("Alice")));
```

### Module
A named, self-describing unit of code introduced in Java 9 (the Java Platform Module System, JPMS). A module declares what packages it exports and what other modules it requires via a `module-info.java` file.

### NullPointerException
A `RuntimeException` thrown when you attempt to use a reference variable that holds `null` — calling a method on it, accessing a field, or indexing it. One of the most common Java runtime errors.
```java
String name = null;
name.length();  // throws NullPointerException
```

### Object
An instance of a class. An object has state (fields), behaviour (methods), and identity (a unique reference in memory).

### OOP
Object-Oriented Programming. A programming paradigm that organises code around objects — data bundled with the methods that operate on it. Java's four OOP pillars are encapsulation, inheritance, polymorphism, and abstraction.

### Optional
A container class introduced in Java 8 (`java.util.Optional`) that may or may not hold a non-null value. It makes the possibility of absence explicit, reducing NullPointerExceptions.
```java
Optional<String> name = Optional.ofNullable(findName());
name.ifPresent(n -> System.out.println("Hello, " + n));
String result = name.orElse("Anonymous");
```

### Package
A namespace that groups related classes. Declared at the top of a source file with `package com.amalitech.project;`. Packages prevent naming conflicts and control access.

### Parameter
A variable declared in a method's signature that receives the value of an argument when the method is called.
```java
void greet(String name) { ... }  // name is the parameter
```

### Polymorphism
The ability of a single interface or reference type to represent different underlying forms. A `Shape` variable can hold a `Circle` or `Rectangle`; calling `area()` invokes the correct version at runtime (dynamic dispatch).

### Primitive type
One of Java's eight built-in value types: `byte`, `short`, `int`, `long`, `float`, `double`, `char`, `boolean`. Primitives store their value directly on the stack, not on the heap.

### Record
A concise class declaration introduced in Java 16 for immutable data carriers. The compiler automatically generates the constructor, getters, `equals()`, `hashCode()`, and `toString()`.
```java
record Point(int x, int y) {}
Point p = new Point(3, 4);
System.out.println(p.x()); // 3
```

### Recursion
A technique where a method calls itself to solve a smaller version of the same problem. Every recursive method needs a base case to stop the recursion.
```java
int factorial(int n) {
    if (n <= 1) return 1;          // base case
    return n * factorial(n - 1);   // recursive call
}
```

### Reference type
Any type that is not a primitive — classes, interfaces, arrays, and enums. A reference-type variable holds the memory address of an object on the heap, not the object's data directly.

### Reflection
The ability of a Java program to examine and modify its own structure (classes, fields, methods) at runtime using the `java.lang.reflect` package. Used by frameworks like Spring and JUnit.
```java
Class<?> clazz = Class.forName("com.example.MyClass");
Method m = clazz.getMethod("doSomething");
m.invoke(instance);
```

### Repository pattern
A design pattern that abstracts data access behind an interface, decoupling business logic from the persistence layer. In Spring Data JPA, extending `JpaRepository` gives you a full repository with minimal code.

### REST
Representational State Transfer. An architectural style for web APIs that uses HTTP methods (GET, POST, PUT, DELETE) to perform operations on resources identified by URLs. Data is typically exchanged as JSON.

### Return type
The type of value a method produces when it finishes executing, declared before the method name. Use `void` when the method produces no value.
```java
int add(int a, int b) { return a + b; }   // return type is int
void print(String s) { System.out.println(s); } // void — no return value
```

### Scope
The region of code where a variable or method is visible and accessible. A variable declared inside a `{}` block only exists within that block.

### Sealed class
A class (or interface) declared with `sealed` (Java 17) that restricts which classes may extend or implement it, listed in a `permits` clause.
```java
sealed interface Shape permits Circle, Rectangle, Triangle {}
```

### Serialization
The process of converting an object's state into a byte stream so it can be saved to a file or sent over a network. The reverse is deserialization. A class must implement `java.io.Serializable` to participate.

### SOLID
Five object-oriented design principles: **S**ingle Responsibility, **O**pen/Closed, **L**iskov Substitution, **I**nterface Segregation, and **D**ependency Inversion. Following SOLID leads to flexible, maintainable code.

### Spring Boot
An opinionated framework built on top of the Spring Framework that makes it fast to create stand-alone, production-grade Java applications. It auto-configures many things and embeds a Tomcat server so you can run your app with `java -jar`.

### SQL
Structured Query Language. The standard language for managing and querying relational databases. Core statements: `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `CREATE TABLE`.

### Stack
In data structures, a last-in first-out (LIFO) collection. In JVM memory, the call stack stores method call frames, local variables, and return addresses. Each thread has its own call stack.

### Stack overflow
A `StackOverflowError` thrown when the JVM call stack runs out of space — most commonly caused by infinite recursion (a recursive method with no reachable base case).

### Static
A modifier meaning a member belongs to the class itself rather than to any instance. A static field is shared by all instances; a static method can be called without creating an object.
```java
class MathUtils {
    static int square(int n) { return n * n; }
}
int result = MathUtils.square(5); // no instance needed
```

### Stream
A sequence of elements from a source (collection, array, I/O channel) that supports aggregate operations in a declarative pipeline style. Introduced in Java 8 (`java.util.stream`). Streams are lazy and do not modify the source.
```java
List<String> names = List.of("Alice", "Bob", "Charlie");
names.stream()
     .filter(n -> n.startsWith("A"))
     .map(String::toUpperCase)
     .forEach(System.out::println);  // ALICE
```

### String pool
A special area of the heap (PermGen in older JVMs, Metaspace/heap in modern ones) where the JVM stores string literals. When two literals have the same value, they share the same pool object to save memory.
```java
String a = "hello";
String b = "hello";
System.out.println(a == b);  // true — same pool object
```

### Synchronization
A mechanism that ensures only one thread can execute a block of code or method at a time, preventing race conditions. Applied with the `synchronized` keyword.
```java
synchronized void increment() { count++; }
```

### TDD
Test-Driven Development. A practice where you write a failing test first, then write the minimum code to make it pass, then refactor. The cycle is Red → Green → Refactor.

### Thread
The smallest unit of execution within a process. Java supports multithreading natively. Each thread has its own call stack but shares the heap with other threads in the same process.
```java
Thread t = new Thread(() -> System.out.println("Running in thread"));
t.start();
```

### Throwable
The root class of Java's error and exception hierarchy. `Error` (for serious JVM problems) and `Exception` (for recoverable conditions) both extend `Throwable`. You can `throw` and `catch` any `Throwable`.

### Type casting
Explicitly converting a value from one type to another. Widening casts (e.g. `int` → `long`) are automatic. Narrowing casts (e.g. `double` → `int`) require an explicit cast and may lose data.
```java
double price = 9.99;
int truncated = (int) price;  // 9 — decimal part lost
```

### Unit test
A test that verifies the behaviour of a single, isolated unit of code (usually one method or one class). Unit tests are fast, do not involve databases or networks, and use mocks for dependencies.

### Unboxing
The automatic conversion from a wrapper class (e.g. `Integer`) to its primitive type (`int`).
```java
Integer boxed = 42;
int primitive = boxed;  // unboxing
```

### var
A local variable type inference keyword introduced in Java 10. The compiler infers the type from the initialiser. Only valid for local variables.
```java
var list = new ArrayList<String>();  // compiler knows it's ArrayList<String>
var name = "Alice";                  // compiler knows it's String
```

### Virtual thread
A lightweight thread introduced in Java 21 (Project Loom) managed by the JVM rather than the OS. Virtual threads make it practical to have millions of concurrent threads, simplifying high-throughput server code.
```java
Thread vt = Thread.ofVirtual().start(() -> System.out.println("Virtual!"));
```

### Void
A return type indicating that a method does not return a value. The `void` keyword is used in method declarations; `Void` (capitalised) is a wrapper class used in generics.

### Wildcard
In Java generics, the `?` symbol represents an unknown type. `List<?>` accepts a list of any type. Bounded wildcards restrict the range: `List<? extends Number>` (upper bound) or `List<? super Integer>` (lower bound).
```java
void printAll(List<?> items) {
    for (Object item : items) System.out.println(item);
}
```
