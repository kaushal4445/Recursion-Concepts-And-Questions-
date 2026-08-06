# Flatten Binary Tree (GFG Version)

## Problem Statement

Given a Binary Tree, flatten it into a linked list.

The linked list should follow the **Inorder Traversal** of the tree.

Every node's

- left pointer = NULL
- right pointer = next node in inorder traversal

Return the head of the flattened list.

---

## Example

Input

```
        1
       / \
      2   5
     / \
    3   4
```

Inorder

```
3 2 4 1 5
```

Output

```
3
 \
  2
   \
    4
     \
      1
       \
        5
```

---

# Approach

We recursively flatten

- Left subtree
- Right subtree

Then

```
Left List

↓

Root

↓

Right List
```

The final linked list follows **Inorder Traversal**.

---

# Recursive Diagram

```
            1

         /     \

       2         5

     /   \

    3     4
```

Flatten Left

```
3 → 2 → 4
```

Flatten Right

```
5
```

Combine

```
3 → 2 → 4 → 1 → 5
```

---

# Algorithm

1. Flatten left subtree.
2. Flatten right subtree.
3. Find last node of left list.
4. Connect last node to root.
5. Connect root to right list.
6. Return head.

---

# Time Complexity

```
O(N²)
```

Finding the tail each time may take O(N).

---

# Space Complexity

```
O(H)
```

H = Height of tree.

---

# Concepts

- Recursion
- Binary Tree
- Inorder Traversal
- Linked List
- Divide and Conquer
