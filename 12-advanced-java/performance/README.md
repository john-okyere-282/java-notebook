# Java Performance

## The Golden Rule

> Premature optimisation is the root of all evil. — Donald Knuth

Write clean, correct, maintainable code first. Optimise only when you have measured a real bottleneck. Guessing at performance issues and optimising speculatively wastes time and produces harder-to-maintain code.

**The process:**
1. Write clear code.
2. Measure — find where the time actually goes.
3. Optimise the bottleneck.
4. Measure again to confirm improvement.

---

## Profiling with VisualVM

VisualVM is a free GUI profiler bundled with the JDK (`jdk/bin/jvisualvm`). It shows:
- **CPU usage** — which methods consume the most CPU time.
- **Heap usage** — how much memory is in use; identify memory leaks.
- **Thread activity** — thread states; detect deadlocks.
- **GC activity** — frequency and duration of garbage collections.

**Basic workflow:**
1. Start your application.
2. Open VisualVM and attach to the running JVM process.
3. Click "Profiler" → "CPU" → "Start".
4. Exercise the slow code path.
5. Click "Stop" → review the "Hot Spots" table.

The hot spots table shows methods sorted by CPU time — optimise the top entries.

---

## Common Performance Mistakes

### 1. String concatenation in a loop

```java
// BAD — creates a new String object on every iteration: O(n²) total work
String result = "";
for (String part : parts) {
    result += part;    // copies the entire string each time
}

// GOOD — StringBuilder uses a resizable buffer: O(n) total work
StringBuilder sb = new StringBuilder();
for (String part : parts) {
    sb.append(part);
}
String result = sb.toString();
```

For 10,000 parts, the `+=` version may be 100x slower than `StringBuilder`.

### 2. Unnecessary object creation

```java
// BAD — creates a new Integer wrapper on every call in a tight loop
List<Integer> numbers = new ArrayList<>();
for (int i = 0; i < 1_000_000; i++) {
    numbers.add(new Integer(i));  // deprecated; also causes boxing overhead
}

// GOOD — autoboxing is automatic, but be aware it happens
// For primitive-heavy work, prefer primitive arrays or IntStream
int[] array = new int[1_000_000];
for (int i = 0; i < array.length; i++) {
    array[i] = i;
}
```

### 3. Using ArrayList when HashSet would be O(1)

```java
List<String> allowList = new ArrayList<>(Arrays.asList("admin", "superuser", "root"));

// BAD — O(n) per contains() call in a loop → O(n²) overall
for (String user : users) {
    if (allowList.contains(user)) { ... }  // scans the whole list each time
}

// GOOD — O(1) per contains() call → O(n) overall
Set<String> allowSet = new HashSet<>(allowList);
for (String user : users) {
    if (allowSet.contains(user)) { ... }
}
```

### 4. Closing streams and resources inside a loop

```java
// BAD — opens and closes the file on every iteration
for (String line : lines) {
    try (FileWriter fw = new FileWriter("log.txt", true)) {
        fw.write(line + "\n");
    }
}

// GOOD — open once, write many, close once
try (BufferedWriter bw = new BufferedWriter(new FileWriter("log.txt", true))) {
    for (String line : lines) {
        bw.write(line);
        bw.newLine();
    }
}
```

### 5. Loading more data than needed

```java
// BAD — loads all 1,000,000 rows into memory
List<Order> allOrders = orderRepository.findAll();
List<Order> recent    = allOrders.stream()
    .filter(o -> o.getDate().isAfter(lastWeek))
    .collect(Collectors.toList());

// GOOD — query returns only the rows you need
List<Order> recent = orderRepository.findByDateAfter(lastWeek);
```

---

## Collections Selection for Performance

Choosing the right collection is often the highest-impact performance decision:

| Need | Use | Reason |
|------|-----|--------|
| Ordered list, random access | `ArrayList` | O(1) get by index |
| Frequent inserts/removes at both ends | `ArrayDeque` | O(1) at head and tail |
| Fast membership check | `HashSet` | O(1) contains |
| Sorted, unique values | `TreeSet` | O(log n) operations; always sorted |
| Fast key-value lookup | `HashMap` | O(1) get/put |
| Sorted key-value lookup | `TreeMap` | O(log n); keys always sorted |
| Insertion-order map | `LinkedHashMap` | Maintains insertion order; O(1) operations |
| Thread-safe map | `ConcurrentHashMap` | Lock striping; better than `Collections.synchronizedMap()` |

---

## Lazy Initialization

Delay the creation of expensive objects until they are first needed.

```java
// EAGER — image is loaded at construction time, even if never displayed
public class Dashboard {
    private final HeavyImage logo = new HeavyImage("logo.png");
}

// LAZY — image is loaded only when needed
public class Dashboard {
    private HeavyImage logo;

    public HeavyImage getLogo() {
        if (logo == null) {
            logo = new HeavyImage("logo.png");
        }
        return logo;
    }
}
```

> **Note:** The simple lazy initialisation above is not thread-safe. For thread-safe lazy initialisation, use the holder idiom or `java.util.concurrent.atomic.AtomicReference` with `compareAndSet`.

---

## When to Optimise

Optimise when:
1. A profiler shows a specific method consuming significant CPU time.
2. A specific data structure is too slow for your measured data size.
3. You have a documented performance requirement (e.g. "< 200ms p99 latency") that you are failing.

Do not optimise:
- Code that runs once at startup.
- Code that handles < 1,000 items.
- Code that is not on a measured hot path.
- Before writing tests (optimised code is harder to change and you will introduce bugs).
