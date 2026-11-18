# Stacks and Queues

## Stack — LIFO (Last In, First Out)

A stack is a collection where the last element added is the first one removed. Think of a stack of plates — you add to the top and take from the top.

**Core operations:**
- `push(item)` — add to the top
- `pop()` — remove and return the top item
- `peek()` — look at the top item without removing
- `isEmpty()` — check if empty

### Use Cases

- Expression evaluation (calculator, compiler)
- Matching brackets `()[]{}` — push opens, pop and match closes
- Undo/redo functionality
- Browser back navigation
- Call stack in function execution (recursion)
- Depth-first search (DFS)

---

## Queue — FIFO (First In, First Out)

A queue is a collection where the first element added is the first one removed. Think of a line at a shop — the person who arrived first is served first.

**Core operations:**
- `enqueue(item)` / `offer(item)` — add to the rear
- `dequeue(item)` / `poll()` — remove and return the front item
- `peek()` — look at the front item without removing
- `isEmpty()` — check if empty

### Use Cases

- Task scheduling (print queue, job queue)
- Breadth-first search (BFS)
- Message queues (producer-consumer)
- Request handling in web servers
- Level-order tree traversal

---

## Java Implementations Using ArrayDeque

The recommended Java implementation for both stacks and queues is `ArrayDeque`. It is backed by a resizable array and offers O(1) amortised operations at both ends. It is faster than `Stack` (which is synchronized and legacy) and faster than `LinkedList` for most use cases.

### Stack Using ArrayDeque

```java
import java.util.ArrayDeque;
import java.util.Deque;

Deque<Integer> stack = new ArrayDeque<>();

// Push
stack.push(10);   // addFirst — adds to the front (top)
stack.push(20);
stack.push(30);

// Peek
System.out.println(stack.peek()); // 30 — does not remove

// Pop
System.out.println(stack.pop());  // 30 — removes and returns top
System.out.println(stack.pop());  // 20

System.out.println(stack.isEmpty()); // false
```

### Queue Using ArrayDeque

```java
Deque<String> queue = new ArrayDeque<>();

// Enqueue (add to back)
queue.offer("Alice");
queue.offer("Bob");
queue.offer("Carol");

// Peek at front
System.out.println(queue.peek()); // "Alice"

// Dequeue (remove from front)
System.out.println(queue.poll()); // "Alice"
System.out.println(queue.poll()); // "Bob"
```

> **Why not `java.util.Stack`?** It extends `Vector`, which is synchronized. Synchronization makes it slow in single-threaded use. Use `ArrayDeque` instead.

---

## Custom Stack Implementation

```java
import java.util.ArrayList;
import java.util.EmptyStackException;
import java.util.List;

public class Stack<T> {

    private final List<T> elements = new ArrayList<>();

    public void push(T item) {
        elements.add(item);
    }

    public T pop() {
        if (isEmpty()) throw new EmptyStackException();
        return elements.remove(elements.size() - 1);
    }

    public T peek() {
        if (isEmpty()) throw new EmptyStackException();
        return elements.get(elements.size() - 1);
    }

    public boolean isEmpty() {
        return elements.isEmpty();
    }

    public int size() {
        return elements.size();
    }

    @Override
    public String toString() {
        return elements.toString();
    }
}
```

---

## Custom Queue Implementation

```java
import java.util.LinkedList;
import java.util.NoSuchElementException;

public class Queue<T> {

    private final LinkedList<T> elements = new LinkedList<>();

    public void enqueue(T item) {
        elements.addLast(item);
    }

    public T dequeue() {
        if (isEmpty()) throw new NoSuchElementException("Queue is empty");
        return elements.removeFirst();
    }

    public T peek() {
        if (isEmpty()) throw new NoSuchElementException("Queue is empty");
        return elements.getFirst();
    }

    public boolean isEmpty() {
        return elements.isEmpty();
    }

    public int size() {
        return elements.size();
    }
}
```

---

## Bracket Matching Example — Stack in Action

```java
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.Map;

public class BracketMatcher {

    private static final Map<Character, Character> PAIRS = Map.of(
        ')', '(',
        ']', '[',
        '}', '{'
    );

    public static boolean isBalanced(String expression) {
        Deque<Character> stack = new ArrayDeque<>();

        for (char ch : expression.toCharArray()) {
            if ("([{".indexOf(ch) >= 0) {
                stack.push(ch);                      // opening bracket — push
            } else if (PAIRS.containsKey(ch)) {
                if (stack.isEmpty() || stack.pop() != PAIRS.get(ch)) {
                    return false;                     // mismatch or unmatched close
                }
            }
        }

        return stack.isEmpty();                       // must be empty if all matched
    }

    public static void main(String[] args) {
        System.out.println(isBalanced("()[]{}"));     // true
        System.out.println(isBalanced("{[()]}"));     // true
        System.out.println(isBalanced("{[(])}"));     // false
        System.out.println(isBalanced("("));          // false
    }
}
```

---

## Priority Queue

A priority queue removes the element with the highest priority (by default, the smallest value) instead of the oldest. Java's `PriorityQueue` is a min-heap.

```java
import java.util.PriorityQueue;

PriorityQueue<Integer> minHeap = new PriorityQueue<>();
minHeap.offer(30);
minHeap.offer(10);
minHeap.offer(20);

System.out.println(minHeap.poll()); // 10 — smallest first
System.out.println(minHeap.poll()); // 20
System.out.println(minHeap.poll()); // 30

// Max-heap using reversed comparator
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(java.util.Collections.reverseOrder());
maxHeap.offer(30);
maxHeap.offer(10);
maxHeap.offer(20);

System.out.println(maxHeap.poll()); // 30 — largest first
```

---

## Complexity Summary

| Operation | ArrayDeque Stack | ArrayDeque Queue | PriorityQueue |
|-----------|----------------|-----------------|---------------|
| Push / Enqueue | O(1) amortised | O(1) amortised | O(log n) |
| Pop / Dequeue | O(1) | O(1) | O(log n) |
| Peek | O(1) | O(1) | O(1) |
