# Linked Lists

## What Is a Linked List?

A linked list is a data structure where each element (node) stores a value and a reference (pointer) to the next node. Unlike an array, elements are not stored in contiguous memory — each node can be anywhere in memory, connected by references.

```
head
 │
 ▼
┌───────────┐    ┌───────────┐    ┌───────────┐
│ value: 10 │───►│ value: 20 │───►│ value: 30 │───► null
└───────────┘    └───────────┘    └───────────┘
```

**Advantages over arrays:**
- O(1) insertion/deletion at the head (no shifting).
- Dynamic size — no need to declare size upfront.

**Disadvantages:**
- O(n) access by index — no random access.
- More memory per element — each node stores a reference.
- Poor cache performance — nodes scattered in memory.

---

## Node Class

```java
public class Node {
    int value;
    Node next;

    Node(int value) {
        this.value = value;
        this.next  = null;
    }
}
```

---

## Singly Linked List Implementation

```java
public class SinglyLinkedList {

    private Node head;
    private int size;

    // ── Insertion ────────────────────────────────────────────────────────────

    /** Insert at the front — O(1) */
    public void insertAtFront(int value) {
        Node newNode = new Node(value);
        newNode.next = head;
        head = newNode;
        size++;
    }

    /** Insert at the end — O(n) */
    public void insertAtEnd(int value) {
        Node newNode = new Node(value);
        if (head == null) {
            head = newNode;
        } else {
            Node current = head;
            while (current.next != null) {
                current = current.next;
            }
            current.next = newNode;
        }
        size++;
    }

    /** Insert after a given value — O(n) */
    public void insertAfter(int targetValue, int newValue) {
        Node current = head;
        while (current != null) {
            if (current.value == targetValue) {
                Node newNode    = new Node(newValue);
                newNode.next    = current.next;
                current.next    = newNode;
                size++;
                return;
            }
            current = current.next;
        }
        throw new NoSuchElementException("Value not found: " + targetValue);
    }

    // ── Deletion ──────────────────────────────────────────────────────────────

    /** Delete the first node with the given value — O(n) */
    public void delete(int value) {
        if (head == null) throw new NoSuchElementException("List is empty");

        if (head.value == value) {
            head = head.next;
            size--;
            return;
        }

        Node current = head;
        while (current.next != null) {
            if (current.next.value == value) {
                current.next = current.next.next;
                size--;
                return;
            }
            current = current.next;
        }
        throw new NoSuchElementException("Value not found: " + value);
    }

    // ── Search ────────────────────────────────────────────────────────────────

    /** Returns true if value is in the list — O(n) */
    public boolean contains(int value) {
        Node current = head;
        while (current != null) {
            if (current.value == value) return true;
            current = current.next;
        }
        return false;
    }

    // ── Traversal ──────────────────────────────────────────────────────────────

    /** Print all values in order */
    public void print() {
        Node current = head;
        StringBuilder sb = new StringBuilder("[");
        while (current != null) {
            sb.append(current.value);
            if (current.next != null) sb.append(", ");
            current = current.next;
        }
        sb.append("]");
        System.out.println(sb);
    }

    /** Return the number of nodes */
    public int size() {
        return size;
    }

    // ── Reversal ──────────────────────────────────────────────────────────────

    /** Reverse the list in-place — O(n), O(1) space */
    public void reverse() {
        Node previous = null;
        Node current  = head;
        while (current != null) {
            Node next    = current.next;
            current.next = previous;
            previous     = current;
            current      = next;
        }
        head = previous;
    }
}
```

---

## Demo

```java
public class LinkedListDemo {
    public static void main(String[] args) {
        SinglyLinkedList list = new SinglyLinkedList();

        list.insertAtEnd(10);
        list.insertAtEnd(20);
        list.insertAtEnd(30);
        list.insertAtFront(5);
        list.print();         // [5, 10, 20, 30]

        list.delete(10);
        list.print();         // [5, 20, 30]

        list.reverse();
        list.print();         // [30, 20, 5]

        System.out.println("Contains 20: " + list.contains(20)); // true
        System.out.println("Size: " + list.size());              // 3
    }
}
```

---

## Complexity Summary

| Operation | Time | Notes |
|-----------|------|-------|
| Insert at front | O(1) | Update head pointer only |
| Insert at end | O(n) | Must traverse to find the last node |
| Insert after value | O(n) | Must find the node |
| Delete by value | O(n) | Must find the predecessor |
| Search (contains) | O(n) | Linear traversal |
| Get by index | O(n) | No random access |
| Reverse | O(n) | Single pass |

---

## Java's Built-in LinkedList

Java's `java.util.LinkedList` is a doubly-linked list (each node has `next` and `previous` references) that implements both `List` and `Deque`.

```java
import java.util.LinkedList;

LinkedList<Integer> list = new LinkedList<>();

list.addFirst(10);   // insert at front — O(1)
list.addLast(20);    // insert at end — O(1) (doubly-linked has tail reference)
list.add(30);        // also adds to end

list.removeFirst();  // O(1)
list.removeLast();   // O(1)
list.get(1);         // O(n) — still linear, despite being a "linked list"

System.out.println(list); // [20]
```

**When to use `LinkedList` vs `ArrayList`:**
- Use `LinkedList` as a **deque** (double-ended queue) when you frequently add/remove from both ends.
- Use `ArrayList` for almost everything else — it has better cache performance for sequential access and O(1) random access.
- Do not use `LinkedList.get(i)` in a loop — it is O(n) per call, making the loop O(n²).
