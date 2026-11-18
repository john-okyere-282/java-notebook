# Trees

## What Is a Binary Tree?

A binary tree is a hierarchical data structure where each node has at most two children, referred to as the **left child** and the **right child**. The topmost node is the **root**.

```
         10          ← root
        /  \
       5    15       ← internal nodes
      / \     \
     3   7     20   ← leaves (no children)
```

**Terminology:**
- **Node** — a single element containing data and references to children.
- **Root** — the top node; has no parent.
- **Leaf** — a node with no children.
- **Height** — the number of edges on the longest path from root to a leaf.
- **Depth** — the number of edges from root to a specific node.

---

## Binary Search Tree (BST)

A BST is a binary tree with an ordering property:

> For every node `n`: all values in `n`'s left subtree are **less than** `n.value`, and all values in `n`'s right subtree are **greater than** `n.value`.

This property makes search, insertion, and deletion efficient.

---

## Node Class

```java
public class TreeNode {
    int value;
    TreeNode left;
    TreeNode right;

    TreeNode(int value) {
        this.value = value;
    }
}
```

---

## BST Implementation

```java
public class BinarySearchTree {

    private TreeNode root;

    // ── Insertion ───────────────────────────────────────────────────────────

    public void insert(int value) {
        root = insertRecursive(root, value);
    }

    private TreeNode insertRecursive(TreeNode node, int value) {
        if (node == null) return new TreeNode(value);

        if (value < node.value) {
            node.left  = insertRecursive(node.left, value);
        } else if (value > node.value) {
            node.right = insertRecursive(node.right, value);
        }
        // Equal value — duplicates not inserted
        return node;
    }

    // ── Search ──────────────────────────────────────────────────────────────

    public boolean contains(int value) {
        return searchRecursive(root, value);
    }

    private boolean searchRecursive(TreeNode node, int value) {
        if (node == null)        return false;
        if (value == node.value) return true;
        if (value < node.value)  return searchRecursive(node.left, value);
        return searchRecursive(node.right, value);
    }

    // ── Deletion ────────────────────────────────────────────────────────────

    public void delete(int value) {
        root = deleteRecursive(root, value);
    }

    private TreeNode deleteRecursive(TreeNode node, int value) {
        if (node == null) return null;

        if (value < node.value) {
            node.left  = deleteRecursive(node.left, value);
        } else if (value > node.value) {
            node.right = deleteRecursive(node.right, value);
        } else {
            // Node to delete found
            if (node.left == null)  return node.right;  // only right child (or none)
            if (node.right == null) return node.left;   // only left child

            // Node has two children: replace with in-order successor (smallest in right subtree)
            int minRight = findMin(node.right);
            node.value   = minRight;
            node.right   = deleteRecursive(node.right, minRight);
        }
        return node;
    }

    private int findMin(TreeNode node) {
        while (node.left != null) node = node.left;
        return node.value;
    }

    // ── Traversal ───────────────────────────────────────────────────────────

    /**
     * In-order traversal: Left → Root → Right
     * Visits nodes in ascending order for a BST.
     */
    public void inOrder() {
        inOrderRecursive(root);
        System.out.println();
    }

    private void inOrderRecursive(TreeNode node) {
        if (node == null) return;
        inOrderRecursive(node.left);
        System.out.print(node.value + " ");
        inOrderRecursive(node.right);
    }

    /**
     * Pre-order traversal: Root → Left → Right
     * Useful for copying or serialising a tree.
     */
    public void preOrder() {
        preOrderRecursive(root);
        System.out.println();
    }

    private void preOrderRecursive(TreeNode node) {
        if (node == null) return;
        System.out.print(node.value + " ");
        preOrderRecursive(node.left);
        preOrderRecursive(node.right);
    }

    /**
     * Post-order traversal: Left → Right → Root
     * Useful for deleting a tree (delete children before parent).
     */
    public void postOrder() {
        postOrderRecursive(root);
        System.out.println();
    }

    private void postOrderRecursive(TreeNode node) {
        if (node == null) return;
        postOrderRecursive(node.left);
        postOrderRecursive(node.right);
        System.out.print(node.value + " ");
    }

    /**
     * Level-order (BFS) traversal — visits nodes level by level.
     */
    public void levelOrder() {
        if (root == null) return;

        java.util.Queue<TreeNode> queue = new java.util.ArrayDeque<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            TreeNode current = queue.poll();
            System.out.print(current.value + " ");

            if (current.left  != null) queue.offer(current.left);
            if (current.right != null) queue.offer(current.right);
        }
        System.out.println();
    }

    // ── Height ──────────────────────────────────────────────────────────────

    public int height() {
        return heightRecursive(root);
    }

    private int heightRecursive(TreeNode node) {
        if (node == null) return -1;  // -1 so a single node has height 0
        return 1 + Math.max(heightRecursive(node.left), heightRecursive(node.right));
    }
}
```

---

## Traversal Visualisation

For the tree:
```
      10
     /  \
    5    15
   / \     \
  3   7     20
```

| Traversal | Order | Output |
|-----------|-------|--------|
| **In-order** | Left → Root → Right | `3 5 7 10 15 20` |
| **Pre-order** | Root → Left → Right | `10 5 3 7 15 20` |
| **Post-order** | Left → Right → Root | `3 7 5 20 15 10` |
| **Level-order** | Level by level | `10 5 15 3 7 20` |

---

## Demo

```java
public class BSTDemo {
    public static void main(String[] args) {
        BinarySearchTree bst = new BinarySearchTree();

        bst.insert(10);
        bst.insert(5);
        bst.insert(15);
        bst.insert(3);
        bst.insert(7);
        bst.insert(20);

        System.out.print("In-order:   "); bst.inOrder();     // 3 5 7 10 15 20
        System.out.print("Pre-order:  "); bst.preOrder();    // 10 5 3 7 15 20
        System.out.print("Post-order: "); bst.postOrder();   // 3 7 5 20 15 10
        System.out.print("Level-order:"); bst.levelOrder();  // 10 5 15 3 7 20

        System.out.println("Contains 7: "  + bst.contains(7));  // true
        System.out.println("Contains 99: " + bst.contains(99)); // false
        System.out.println("Height: "      + bst.height());     // 2

        bst.delete(5);
        System.out.print("After deleting 5: "); bst.inOrder(); // 3 7 10 15 20
    }
}
```

---

## BST Complexity

| Operation | Average | Worst case (unbalanced) |
|-----------|---------|------------------------|
| Insert | O(log n) | O(n) |
| Search | O(log n) | O(n) |
| Delete | O(log n) | O(n) |

Worst case occurs when elements are inserted in sorted order (the tree degenerates into a linked list). Self-balancing BSTs (AVL tree, Red-Black tree) guarantee O(log n) in all cases. Java's `TreeMap` and `TreeSet` use a Red-Black tree internally.
