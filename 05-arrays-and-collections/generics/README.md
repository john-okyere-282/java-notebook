# Generics

## Why Generics?
Before generics (Java 1.4 and earlier), collections held `Object` — you had to cast every element you retrieved, and the compiler couldn't catch type mistakes.

```java
// Without generics (old way) — unsafe
List list = new ArrayList();
list.add("hello");
list.add(42);                        // allowed — no compile error
String s = (String) list.get(1);     // ClassCastException at runtime! (it's an int)

// With generics — type-safe
List<String> strings = new ArrayList<>();
strings.add("hello");
// strings.add(42);                  // compile error — only Strings allowed
String s = strings.get(0);           // no cast needed — always a String
```

Generics move type errors from **runtime** to **compile time** — you catch bugs earlier.

---

## Generic Syntax

The type parameter `<T>` (or `<K, V>` for maps) is a placeholder for the actual type supplied by the caller.

```java
// Declaring — use with the class
List<String>           strings  = new ArrayList<>();
Map<String, Integer>   scores   = new HashMap<>();
Set<Double>            values   = new HashSet<>();
Queue<Task>            taskQueue = new ArrayDeque<>();
```

The convention for type parameter names:
| Letter | Meaning |
|--------|---------|
| `T` | Type (general) |
| `E` | Element (collections) |
| `K` | Key (maps) |
| `V` | Value (maps) |
| `N` | Number |
| `R` | Return type |

---

## Generic Classes

You can define your own generic classes:

```java
public class Box<T> {
    private T content;

    public Box(T content) {
        this.content = content;
    }

    public T get() {
        return content;
    }

    public void set(T content) {
        this.content = content;
    }

    @Override
    public String toString() {
        return "Box[" + content + "]";
    }
}

Box<String>  textBox   = new Box<>("Hello");
Box<Integer> numberBox = new Box<>(42);
Box<Double>  priceBox  = new Box<>(9.99);

System.out.println(textBox.get());    // Hello
System.out.println(numberBox.get());  // 42
System.out.println(priceBox);         // Box[9.99]
```

---

## Generic Methods

A method can have its own type parameter, independent of the class's:

```java
// Returns the first element of any list
public static <T> T first(List<T> list) {
    if (list.isEmpty()) return null;
    return list.get(0);
}

// Swaps two elements in an array
public static <T> void swap(T[] array, int i, int j) {
    T temp = array[i];
    array[i] = array[j];
    array[j] = temp;
}

// Usage
String firstFruit = first(List.of("apple", "banana", "mango"));  // apple
Integer firstNum  = first(List.of(10, 20, 30));                   // 10
```

---

## Generic Pairs and Tuples

```java
public class Pair<A, B> {
    private final A first;
    private final B second;

    public Pair(A first, B second) {
        this.first = first;
        this.second = second;
    }

    public A getFirst()  { return first; }
    public B getSecond() { return second; }

    @Override
    public String toString() {
        return "(" + first + ", " + second + ")";
    }
}

Pair<String, Integer> nameAge = new Pair<>("Alice", 30);
Pair<Double, Double>  coords  = new Pair<>(52.37, 4.90);

System.out.println(nameAge);   // (Alice, 30)
System.out.println(coords);    // (52.37, 4.9)
```

---

## Bounded Type Parameters

You can restrict what types are allowed:

```java
// Upper bound: T must be a Number or subtype
public static <T extends Number> double sum(List<T> list) {
    double total = 0;
    for (T item : list) {
        total += item.doubleValue();   // .doubleValue() is on Number
    }
    return total;
}

System.out.println(sum(List.of(1, 2, 3)));       // 6.0  (Integer list)
System.out.println(sum(List.of(1.5, 2.5, 3.0))); // 7.0  (Double list)
// sum(List.of("a", "b"));  // compile error — String doesn't extend Number
```

---

## Wildcards

Wildcards let you write methods that work with generic types you don't know exactly:

```java
// Upper-bounded wildcard: accepts List<Integer>, List<Double>, List<Number>, etc.
public static void printNumbers(List<? extends Number> numbers) {
    for (Number n : numbers) {
        System.out.println(n);
    }
}

// Lower-bounded wildcard: accepts List<Number>, List<Object>
public static void addNumbers(List<? super Integer> list) {
    list.add(1);
    list.add(2);
    list.add(3);
}

// Unbounded wildcard: any type
public static void printAll(List<?> list) {
    for (Object item : list) {
        System.out.println(item);
    }
}
```

**PECS rule:** Producer Extends, Consumer Super.
- If you are **reading from** a collection (it produces values) → `? extends T`
- If you are **writing to** a collection (it consumes values) → `? super T`

---

## Type Erasure

At runtime, generics are erased — `List<String>` and `List<Integer>` both become `List` at the bytecode level. This is why you cannot do `new T()` or `instanceof List<String>`. Generics are purely a compile-time safety feature.

---

## Exercises

**Exercise 1:** Create a generic `Stack<T>` class using an `ArrayList<T>` internally. Implement `push(T item)`, `pop()`, `peek()`, `isEmpty()`, and `size()`. Test with `Stack<String>` and `Stack<Integer>`.

**Exercise 2:** Write a generic method `findMax(List<T extends Comparable<T>>)` that returns the largest element in the list. Test with a list of integers and a list of strings.

**Exercise 3:** Create a generic `Pair<A, B>` class (as shown above). Write a generic method `swap(Pair<A, B>)` that returns a new `Pair<B, A>`. Test it.
