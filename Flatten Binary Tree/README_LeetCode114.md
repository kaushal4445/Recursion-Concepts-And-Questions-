# LeetCode 114 - Flatten Binary Tree to Linked List

## Problem Statement

Flatten the binary tree into a linked list **in-place**.

The linked list must follow **Preorder Traversal**.

```
Root

↓

Left

↓

Right
```

Every node's

- left = NULL
- right = next preorder node

Do NOT return anything.

Function

```cpp
void flatten(TreeNode* root)
```

---

# Example

Input

```
        1
       / \
      2   5
     / \   \
    3   4   6
```

Output

```
1
 \
  2
   \
    3
     \
      4
       \
        5
         \
          6
```

---

# Recursive Idea

Process nodes in Reverse Preorder

```
Right

↓

Left

↓

Root
```

Maintain

```
prev
```

Diagram

```
Flatten(6)

↓

prev = 6

Flatten(5)

↓

5 → 6

Flatten(4)

↓

4 → 5 → 6
```

Finally

```
1 → 2 → 3 → 4 → 5 → 6
```

---

# Algorithm

```
Flatten Right

↓

Flatten Left

↓

root->right = prev

↓

root->left = NULL

↓

prev = root
```

---

# Complexity

Time

```
O(N)
```

Space

```
O(H)
```

---

# Concepts

- Binary Tree
- DFS
- Reverse Preorder
- Recursion
- In-place Tree Modification
