# Reflection

## What Is Reflection?

Reflection is the ability of a program to inspect and modify its own structure at runtime. With reflection you can:
- Discover what fields, methods, and constructors a class has.
- Read and write field values without calling getters/setters.
- Call methods by name without knowing them at compile time.
- Create instances of classes by name.

Reflection is the foundation of Spring's dependency injection, Hibernate's ORM mapping, JUnit's test runner, and serialization libraries.

---

## Getting a Class Object

```java
// From a class literal
Class<String> strClass = String.class;

// From an instance
String s = "hello";
Class<?> strClass2 = s.getClass();

// From a fully-qualified name (throws ClassNotFoundException if not found)
Class<?> dynamicClass = Class.forName("java.util.ArrayList");
```

---

## Inspecting a Class

```java
Class<?> clazz = String.class;

// Class name
System.out.println(clazz.getName());          // java.lang.String
System.out.println(clazz.getSimpleName());    // String

// Fields
for (java.lang.reflect.Field field : clazz.getDeclaredFields()) {
    System.out.println(field.getName() + " : " + field.getType().getSimpleName());
}

// Methods
for (java.lang.reflect.Method method : clazz.getMethods()) {
    System.out.println(method.getName());
}

// Constructors
for (java.lang.reflect.Constructor<?> ctor : clazz.getConstructors()) {
    System.out.println(ctor);
}
```

`getDeclaredFields()` returns all fields (including private ones) declared by the class.  
`getFields()` returns only public fields, including inherited ones.

---

## Calling Methods by Name

```java
import java.lang.reflect.Method;

String s = "Hello, World!";
Method method = String.class.getMethod("substring", int.class, int.class);
String result = (String) method.invoke(s, 7, 12);  // "World"
System.out.println(result);
```

`getMethod()` finds a public method (including inherited ones).  
`getDeclaredMethod()` finds a method declared in the class (including private ones).

---

## Accessing Private Fields

```java
import java.lang.reflect.Field;

class Secret {
    private String password = "hunter2";
}

Secret secret = new Secret();
Field field = Secret.class.getDeclaredField("password");
field.setAccessible(true);  // bypass access check

String value = (String) field.get(secret);
System.out.println(value);  // hunter2

field.set(secret, "newpassword");  // modify the private field
```

> **Warning:** `setAccessible(true)` bypasses access control. In Java 16+, this requires `--add-opens` JVM flags for encapsulated modules. Use only when necessary.

---

## Creating Instances Dynamically

```java
// No-arg constructor
Class<?> clazz = Class.forName("java.util.ArrayList");
Object list = clazz.getDeclaredConstructor().newInstance();

// Constructor with parameters
Class<?> stringClass = String.class;
java.lang.reflect.Constructor<?> ctor = stringClass.getConstructor(String.class);
String s = (String) ctor.newInstance("Hello via reflection");
```

---

## Practical Use Cases

| Use Case | How Reflection Helps |
|----------|---------------------|
| Dependency injection (Spring) | Finds `@Autowired` fields and injects dependencies at runtime |
| ORM (Hibernate) | Maps database columns to Java fields by name |
| Serialization (Jackson) | Reads field names and values to build JSON |
| Test runners (JUnit) | Finds methods annotated `@Test` and invokes them |
| Plugin systems | Loads user-defined classes by name and calls a known interface method |

---

## Performance Warning

Reflection is significantly slower than direct code:
- Method lookup involves hash table lookups and security checks.
- `invoke()` bypasses JIT inline optimisations.
- Microbenchmarks show reflection to be 10–100x slower than direct calls for small, tight loops.

**Rules:**
1. Never use reflection in a hot path (inner loop, per-request code).
2. Cache `Method`, `Field`, and `Constructor` objects — looking them up is the expensive part.
3. For most use cases, use an interface or strategy pattern instead of reflection.
4. When you must use reflection (framework code, plugins), cache aggressively.

```java
// BAD — looks up method on every call
for (Object obj : largeList) {
    Method m = obj.getClass().getMethod("process");
    m.invoke(obj);
}

// BETTER — look up once, reuse
Method m = SomeClass.class.getMethod("process");
for (Object obj : largeList) {
    m.invoke(obj);
}
```
