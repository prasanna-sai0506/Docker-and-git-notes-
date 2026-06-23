# AVL Tree — Complete Guide with Java Implementation

---

## 1. What is an AVL Tree?

An **AVL Tree** (Adelson-Velsky and Landis Tree) is a **self-balancing Binary Search Tree (BST)** where the difference in heights between the left and right subtrees of **any node** is at most **1**.

> Named after Soviet inventors **G.M. Adelson-Velsky** and **E.M. Landis** (1962).

---

## 2. Why AVL Tree?

A plain BST can degenerate into a **linked list** in worst case (sorted input), giving O(n) for search/insert/delete.

| Operation | BST (worst) | AVL Tree (always) |
|-----------|-------------|-------------------|
| Search    | O(n)        | O(log n)          |
| Insert    | O(n)        | O(log n)          |
| Delete    | O(n)        | O(log n)          |

AVL Trees **guarantee O(log n)** by maintaining balance after every insert and delete.

---

## 3. Key Concepts

### 3.1 Height of a Node
Height = number of edges on the **longest path** from that node to a leaf.
- Height of `null` = **-1**
- Height of a leaf = **0**

### 3.2 Balance Factor
```
Balance Factor (BF) = Height(Left Subtree) - Height(Right Subtree)
```

| BF Value | Meaning              |
|----------|----------------------|
| -1, 0, 1 | Balanced ✅           |
| > 1      | Left-heavy ❌ (fix)   |
| < -1     | Right-heavy ❌ (fix)  |

### 3.3 AVL Property
Every node in the tree must satisfy: **-1 ≤ BF ≤ 1**

---

## 4. Rotations

Rotations are the mechanism to **restore balance**. There are 4 cases:

---

### 4.1 Right Rotation (LL Case)
**Triggered when:** Left child is left-heavy (BF > 1 and left child BF ≥ 0)

```
Before:
        z
       /
      y
     /
    x

After Right Rotation at z:
      y
     / \
    x   z
```

---

### 4.2 Left Rotation (RR Case)
**Triggered when:** Right child is right-heavy (BF < -1 and right child BF ≤ 0)

```
Before:
    z
     \
      y
       \
        x

After Left Rotation at z:
      y
     / \
    z   x
```

---

### 4.3 Left-Right Rotation (LR Case)
**Triggered when:** Left child is right-heavy (BF > 1 and left child BF < 0)

```
Step 1 — Left Rotate left child:
        z               z
       /               /
      y       →       x
       \             /
        x           y

Step 2 — Right Rotate z:
      x
     / \
    y   z
```

---

### 4.4 Right-Left Rotation (RL Case)
**Triggered when:** Right child is left-heavy (BF < -1 and right child BF > 0)

```
Step 1 — Right Rotate right child:
    z               z
     \               \
      y       →       x
     /                 \
    x                   y

Step 2 — Left Rotate z:
      x
     / \
    z   y
```

---

### Rotation Summary Table

| Case | Condition                         | Fix                       |
|------|-----------------------------------|---------------------------|
| LL   | BF > 1, left child BF ≥ 0        | Right Rotate              |
| RR   | BF < -1, right child BF ≤ 0      | Left Rotate               |
| LR   | BF > 1, left child BF < 0        | Left Rotate + Right Rotate|
| RL   | BF < -1, right child BF > 0      | Right Rotate + Left Rotate|

---

## 5. Java Implementation

```java
public class AVLTree {

    // ─────────────────────────────────────────────
    // Node Structure
    // ─────────────────────────────────────────────
    static class Node {
        int data, height;
        Node left, right;

        Node(int data) {
            this.data = data;
            this.height = 0; // leaf node height = 0
        }
    }

    private Node root;

    // ─────────────────────────────────────────────
    // Height Utility
    // ─────────────────────────────────────────────
    private int height(Node node) {
        return (node == null) ? -1 : node.height;
    }

    private void updateHeight(Node node) {
        node.height = 1 + Math.max(height(node.left), height(node.right));
    }

    // ─────────────────────────────────────────────
    // Balance Factor
    // ─────────────────────────────────────────────
    private int getBalanceFactor(Node node) {
        return (node == null) ? 0 : height(node.left) - height(node.right);
    }

    // ─────────────────────────────────────────────
    // Rotations
    // ─────────────────────────────────────────────

    // Right Rotation (LL Case)
    private Node rightRotate(Node z) {
        Node y = z.left;
        Node T3 = y.right;

        // Perform rotation
        y.right = z;
        z.left = T3;

        // Update heights (z first, then y)
        updateHeight(z);
        updateHeight(y);

        return y; // new root of this subtree
    }

    // Left Rotation (RR Case)
    private Node leftRotate(Node z) {
        Node y = z.right;
        Node T2 = y.left;

        // Perform rotation
        y.left = z;
        z.right = T2;

        // Update heights
        updateHeight(z);
        updateHeight(y);

        return y; // new root of this subtree
    }

    // ─────────────────────────────────────────────
    // Balance the node
    // ─────────────────────────────────────────────
    private Node balance(Node node) {
        updateHeight(node);
        int bf = getBalanceFactor(node);

        // LL Case
        if (bf > 1 && getBalanceFactor(node.left) >= 0) {
            return rightRotate(node);
        }

        // LR Case
        if (bf > 1 && getBalanceFactor(node.left) < 0) {
            node.left = leftRotate(node.left);
            return rightRotate(node);
        }

        // RR Case
        if (bf < -1 && getBalanceFactor(node.right) <= 0) {
            return leftRotate(node);
        }

        // RL Case
        if (bf < -1 && getBalanceFactor(node.right) > 0) {
            node.right = rightRotate(node.right);
            return leftRotate(node);
        }

        return node; // already balanced
    }

    // ─────────────────────────────────────────────
    // Insert
    // ─────────────────────────────────────────────
    public void insert(int data) {
        root = insertRec(root, data);
    }

    private Node insertRec(Node node, int data) {
        // 1. Standard BST insert
        if (node == null) return new Node(data);

        if (data < node.data)
            node.left = insertRec(node.left, data);
        else if (data > node.data)
            node.right = insertRec(node.right, data);
        else
            return node; // duplicates not allowed

        // 2. Balance and return
        return balance(node);
    }

    // ─────────────────────────────────────────────
    // Delete
    // ─────────────────────────────────────────────
    public void delete(int data) {
        root = deleteRec(root, data);
    }

    private Node deleteRec(Node node, int data) {
        // 1. Standard BST delete
        if (node == null) return null;

        if (data < node.data)
            node.left = deleteRec(node.left, data);
        else if (data > node.data)
            node.right = deleteRec(node.right, data);
        else {
            // Node to delete found
            if (node.left == null) return node.right;
            if (node.right == null) return node.left;

            // Two children: replace with inorder successor (min of right subtree)
            Node successor = getMin(node.right);
            node.data = successor.data;
            node.right = deleteRec(node.right, successor.data);
        }

        // 2. Balance and return
        return balance(node);
    }

    // Find minimum node in a subtree
    private Node getMin(Node node) {
        while (node.left != null) node = node.left;
        return node;
    }

    // ─────────────────────────────────────────────
    // Search
    // ─────────────────────────────────────────────
    public boolean search(int data) {
        return searchRec(root, data);
    }

    private boolean searchRec(Node node, int data) {
        if (node == null) return false;
        if (data == node.data) return true;
        return data < node.data
            ? searchRec(node.left, data)
            : searchRec(node.right, data);
    }

    // ─────────────────────────────────────────────
    // Traversals
    // ─────────────────────────────────────────────

    // Inorder: Left → Root → Right (gives sorted output)
    public void inorder() {
        inorderRec(root);
        System.out.println();
    }

    private void inorderRec(Node node) {
        if (node == null) return;
        inorderRec(node.left);
        System.out.print(node.data + " ");
        inorderRec(node.right);
    }

    // Preorder: Root → Left → Right
    public void preorder() {
        preorderRec(root);
        System.out.println();
    }

    private void preorderRec(Node node) {
        if (node == null) return;
        System.out.print(node.data + " ");
        preorderRec(node.left);
        preorderRec(node.right);
    }

    // ─────────────────────────────────────────────
    // Main — Driver Code
    // ─────────────────────────────────────────────
    public static void main(String[] args) {
        AVLTree tree = new AVLTree();

        // Insert
        int[] values = {10, 20, 30, 40, 50, 25};
        for (int v : values) tree.insert(v);

        System.out.print("Inorder after inserts : ");
        tree.inorder(); // → 10 20 25 30 40 50

        System.out.print("Preorder after inserts: ");
        tree.preorder(); // → 30 20 10 25 40 50

        // Search
        System.out.println("Search 25 : " + tree.search(25)); // true
        System.out.println("Search 99 : " + tree.search(99)); // false

        // Delete
        tree.delete(40);
        System.out.print("Inorder after delete 40: ");
        tree.inorder(); // → 10 20 25 30 50
    }
}
```

---

## 6. Dry Run — Insert {10, 20, 30}

```
Insert 10:
    10 (BF=0) ✅

Insert 20:
    10
      \
      20  (BF of 10 = -1) ✅

Insert 30:
    10
      \
      20
        \
        30  → BF of 10 = -2 ❌  RR Case → Left Rotate at 10

After Left Rotate:
      20
     /  \
    10  30  ✅  (all BF = 0)
```

---

## 7. Complexity Summary

| Operation       | Time       | Space      |
|-----------------|------------|------------|
| Insert          | O(log n)   | O(log n)*  |
| Delete          | O(log n)   | O(log n)*  |
| Search          | O(log n)   | O(log n)*  |
| Space (tree)    | —          | O(n)       |

> *Stack space due to recursion (depth = O(log n))

---

## 8. AVL vs Other Trees

| Feature             | BST         | AVL Tree    | Red-Black Tree |
|---------------------|-------------|-------------|----------------|
| Height guarantee    | No          | O(log n)    | O(log n)       |
| Balance strictness  | None        | Strict (±1) | Relaxed        |
| Insert/Delete speed | O(n) worst  | O(log n)    | O(log n)       |
| Rotations per op    | 0           | O(log n)    | O(1) amortized |
| Best use case       | Random data | Read-heavy  | Write-heavy    |

---

## 9. Key Takeaways

- AVL Trees are **height-balanced BSTs** — BF ∈ {-1, 0, 1} at every node.
- Balance is restored using **4 rotation types**: LL, RR, LR, RL.
- **Height is updated bottom-up** after every insert/delete.
- Always **update height of children before parent** during rotations.
- Inorder traversal of an AVL Tree always gives **sorted output**.
- Preferred over plain BST whenever **worst-case performance** matters.
