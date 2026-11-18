# Queues and Stacks

## Overview
Queues and stacks are collections with specific access patterns:
- **Queue (FIFO):** First In, First Out — like a queue at a shop. The first person in line is served first.
- **Stack (LIFO):** Last In, First Out — like a stack of plates. The last plate placed on top is the first one taken off.

Java's recommended implementation for both is `ArrayDeque`.

---

## Queue (FIFO — First In, First Out)

```java
import java.util.ArrayDeque;
import java.util.Queue;

Queue<String> queue = new ArrayDeque<>();

// Add to the back
queue.offer("First");
queue.offer("Second");
queue.offer("Third");

System.out.println(queue);           // [First, Second, Third]

// Look at the front without removing
System.out.println(queue.peek());    // First

// Remove from the front
System.out.println(queue.poll());    // First  (also removes it)
System.out.println(queue.poll());    // Second
System.out.println(queue);           // [Third]

// poll() returns null if empty; remove() throws NoSuchElementException
// peek() returns null if empty; element() throws NoSuchElementException
```

**Practical use case:** Processing tasks in the order they were submitted (print queue, breadth-first search, task scheduler).

---

## Stack (LIFO — Last In, First Out)

Use `ArrayDeque` as a stack (not the legacy `java.util.Stack` class — it is slow and poorly designed):

```java
import java.util.ArrayDeque;
import java.util.Deque;

Deque<String> stack = new ArrayDeque<>();

// Push onto the top
stack.push("First");
stack.push("Second");
stack.push("Third");

System.out.println(stack);           // [Third, Second, First]

// Look at the top without removing
System.out.println(stack.peek());    // Third

// Pop from the top
System.out.println(stack.pop());     // Third  (removes it)
System.out.println(stack.pop());     // Second
System.out.println(stack);           // [First]
```

**Practical use case:** Undo/redo history, checking balanced brackets, depth-first search, call stack simulation.

---

## Balanced Brackets Example

A classic stack problem:

```java
public static boolean isBalanced(String expression) {
    Deque<Character> stack = new ArrayDeque<>();

    for (char c : expression.toCharArray()) {
        if (c == '(' || c == '[' || c == '{') {
            stack.push(c);           // push opening brackets
        } else if (c == ')' || c == ']' || c == '}') {
            if (stack.isEmpty()) return false;
            char top = stack.pop();
            if (c == ')' && top != '(') return false;
            if (c == ']' && top != '[') return false;
            if (c == '}' && top != '{') return false;
        }
    }

    return stack.isEmpty();  // balanced if nothing left over
}

System.out.println(isBalanced("({[]})"));   // true
System.out.println(isBalanced("({[})"));    // false
System.out.println(isBalanced("((()"));     // false
```

---

## PriorityQueue (Priority-Based Ordering)

A `PriorityQueue` processes elements in **priority order** rather than insertion order. By default, the smallest element is processed first.

```java
import java.util.PriorityQueue;

PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(30);
pq.offer(10);
pq.offer(20);

System.out.println(pq.poll());  // 10 (smallest first)
System.out.println(pq.poll());  // 20
System.out.println(pq.poll());  // 30

// Max priority queue (largest first)
PriorityQueue<Integer> maxPQ = new PriorityQueue<>(Collections.reverseOrder());
maxPQ.offer(10);
maxPQ.offer(30);
maxPQ.offer(20);

System.out.println(maxPQ.poll());  // 30
```

**Practical use case:** Dijkstra's shortest path, Huffman coding, task scheduling by priority.

---

## Deque — Double-Ended Queue

`Deque` (pronounced "deck") lets you add and remove from both ends:

```java
Deque<String> deque = new ArrayDeque<>();

deque.offerFirst("B");   // add to front
deque.offerLast("C");    // add to back
deque.offerFirst("A");   // add to front

System.out.println(deque);  // [A, B, C]

System.out.println(deque.pollFirst());  // A
System.out.println(deque.pollLast());   // C
System.out.println(deque);              // [B]
```

---

## Queue Method Summary

| Method | Queue behaviour | Stack behaviour | Throws if empty? |
|--------|----------------|-----------------|-----------------|
| `offer(e)` / `push(e)` | Add to back | Add to top | No |
| `poll()` / `pop()` | Remove from front | Remove from top | No (`null`) / Yes |
| `peek()` | Look at front | Look at top | No (`null`) |
| `element()` | Look at front | — | Yes |
| `remove()` | Remove from front | — | Yes |

---

## Legacy Stack Class (Avoid)

```java
// DON'T USE — legacy, slow, extends Vector which has synchronisation overhead
Stack<String> old = new Stack<>();

// DO USE — fast, modern
Deque<String> modern = new ArrayDeque<>();
```

---

## Exercises

**Exercise 1:** Simulate a print queue. Add 5 print jobs with names like "Report.pdf", "Invoice.docx". Process (poll and print) them one by one in order.

**Exercise 2:** Implement a simple undo/redo feature using two stacks. `do(String action)` pushes to the undo stack. `undo()` pops from undo and pushes to redo. `redo()` pops from redo and pushes back to undo. Print the current state after each operation.

**Exercise 3:** Use a `PriorityQueue` to find the top 3 highest scores in a list of integers. Add all scores to the queue (using max order) and poll three times.
