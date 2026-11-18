# Maps

## What is a Map?
A `Map` stores **key-value pairs**. Keys are unique — each key maps to exactly one value. You look up values by key, not by position.

**Real-world analogy:** A dictionary. Each word (key) maps to a definition (value). There is only one entry per word. You look up the word to find its definition.

---

## Basic HashMap Usage

```java
import java.util.HashMap;
import java.util.Map;

Map<String, Integer> scores = new HashMap<>();

// Add entries
scores.put("Alice", 95);
scores.put("Bob", 87);
scores.put("Charlie", 92);

// Retrieve
System.out.println(scores.get("Alice"));           // 95
System.out.println(scores.get("Diana"));           // null (key doesn't exist)
System.out.println(scores.getOrDefault("Diana", 0)); // 0 (safe default)

// Check presence
System.out.println(scores.containsKey("Bob"));     // true
System.out.println(scores.containsValue(92));      // true

// Update
scores.put("Bob", 90);    // replaces existing value

// Remove
scores.remove("Charlie");

System.out.println(scores.size());   // 2
System.out.println(scores);          // {Alice=95, Bob=90}  (order not guaranteed)
```

---

## Iterating Over a Map

```java
Map<String, Integer> scores = new HashMap<>(Map.of("Alice", 95, "Bob", 87, "Charlie", 92));

// Method 1: entrySet() — both key and value
for (Map.Entry<String, Integer> entry : scores.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}

// Method 2: keySet() — just keys
for (String name : scores.keySet()) {
    System.out.println(name + " → " + scores.get(name));
}

// Method 3: values() — just values
for (int score : scores.values()) {
    System.out.println(score);
}

// Method 4: forEach with lambda (Java 8+, cleanest)
scores.forEach((name, score) ->
    System.out.println(name + ": " + score)
);
```

---

## Common Methods

| Method | What it does |
|--------|-------------|
| `put(K key, V value)` | Add or update entry |
| `get(K key)` | Returns value or `null` |
| `getOrDefault(K key, V default)` | Returns value or `default` if key absent |
| `remove(K key)` | Remove entry |
| `containsKey(K key)` | True if key exists |
| `containsValue(V value)` | True if value exists |
| `size()` | Number of entries |
| `isEmpty()` | True if no entries |
| `keySet()` | Set of all keys |
| `values()` | Collection of all values |
| `entrySet()` | Set of key-value pairs |
| `putIfAbsent(K key, V value)` | Add only if key not already present |
| `computeIfAbsent(K key, Function)` | Compute and add value only if key absent |
| `merge(K key, V value, BiFunction)` | Merge with existing value |
| `forEach(BiConsumer)` | Iterate with lambda |

---

## Useful Patterns

### Word frequency counter
```java
String text = "apple banana apple mango banana apple";
Map<String, Integer> freq = new HashMap<>();

for (String word : text.split(" ")) {
    freq.put(word, freq.getOrDefault(word, 0) + 1);
}

System.out.println(freq);  // {apple=3, banana=2, mango=1}
```

### Group items by category
```java
List<String> words = List.of("ant", "bear", "ape", "bee", "alligator");
Map<Character, List<String>> byLetter = new HashMap<>();

for (String word : words) {
    char first = word.charAt(0);
    byLetter.computeIfAbsent(first, k -> new ArrayList<>()).add(word);
}

System.out.println(byLetter);
// {a=[ant, ape, alligator], b=[bear, bee]}
```

### Counting unique values
```java
Map<String, Integer> counter = new HashMap<>();
// Increment count (or start at 1 if not present)
counter.merge("java", 1, Integer::sum);
counter.merge("java", 1, Integer::sum);
counter.merge("python", 1, Integer::sum);
System.out.println(counter);  // {java=2, python=1}
```

---

## Immutable Maps (Java 9+)

```java
// Map.of() — up to 10 entries
Map<String, Integer> colours = Map.of("Red", 1, "Green", 2, "Blue", 3);

// Map.copyOf() — immutable copy of an existing map
Map<String, Integer> copy = Map.copyOf(colours);
// copy.put("Yellow", 4);  // throws UnsupportedOperationException
```

---

## HashMap vs LinkedHashMap vs TreeMap

| | `HashMap` | `LinkedHashMap` | `TreeMap` |
|---|---|---|---|
| Order | None | Insertion order | Key sorted (natural) |
| `get`, `put`, `remove` | O(1) avg | O(1) avg | O(log n) |
| Null keys | 1 null key | 1 null key | ❌ No null keys |
| Null values | Allowed | Allowed | Allowed |
| Use when | Fastest lookup | Ordered + fast | Sorted keys |

```java
// TreeMap — keys sorted alphabetically
Map<String, Integer> sorted = new TreeMap<>();
sorted.put("Charlie", 92);
sorted.put("Alice", 95);
sorted.put("Bob", 87);

for (String name : sorted.keySet()) {
    System.out.println(name);  // Alice, Bob, Charlie (sorted!)
}
```

---

## Exercises

**Exercise 1:** Build a word frequency counter. Take a sentence, split it into words, and count how many times each word appears. Print the results sorted alphabetically.

**Exercise 2:** Create a phone book as a `Map<String, String>` (name → phone number). Add 5 entries. Write a lookup method that returns the number or "Not found".

**Exercise 3:** Given a `List<String>` of names, group them by their first letter using a `Map<Character, List<String>>`. Print each group.

**Exercise 4:** Given two maps representing inventory counts, merge them so that if a key appears in both, the values are summed.

**Exercise 5:** Create a grade book as a `Map<String, List<Integer>>` (student name → list of scores). Add multiple scores per student. Write a method that prints each student's name and average score, sorted by student name.
