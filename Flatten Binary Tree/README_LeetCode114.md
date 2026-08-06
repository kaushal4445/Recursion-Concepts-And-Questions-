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


## Why `return head;` Does Not Work in LeetCode?

One of the biggest differences between the GFG and LeetCode versions is the **function signature**.

### GFG Version

The function returns the head of the flattened linked list.

```cpp
Node* flatten(Node* root)
{
    ...
    return head;
}
```

Since the function returns a `Node*`, returning `head` is perfectly valid.

**Visualization**

```
Flatten Left Tree
        │
        ▼
      head (3)
        │
        ▼
3 → 2 → 4 → 1 → 5

Return head
        │
        ▼
Caller receives pointer to node 3
```

The caller uses this returned pointer to connect different parts of the flattened tree.

---

### LeetCode Version

The function signature is

```cpp
void flatten(TreeNode* root)
```

Notice the return type is **`void`**.

A `void` function **does not return any value**.

So writing

```cpp
return head;
```

produces a compilation error because `head` is a `TreeNode*`, while the function is expected to return **nothing**.

Compiler Error

```text
error: void function 'flatten' should not return a value
```

---

## Why This Happens

Your GFG logic assumes that every recursive call returns the head of the flattened subtree.

Example:

```cpp
TreeNode* head = flatten(root->left);
```

Expected flow:

```
flatten(left subtree)

        │
        ▼
Returns head

        │
        ▼
Store in head
```

But in LeetCode,

```cpp
flatten(root->left);
```

returns **nothing**.

So this becomes

```
flatten(left subtree)

        │
        ▼
      void

        │
        ▼
TreeNode* head = void   ❌ Invalid
```

Similarly,

```cpp
root->right = flatten(root->right);
```

is also invalid because

```
flatten(root->right)

        │
        ▼
      void

        │
        ▼
root->right = void   ❌ Invalid
```

Finally,

```cpp
return head;
```

is invalid because the function must return nothing.

```
Function Type

void flatten(...)

        │
        ▼
Expected Return

Nothing

        │
        ▼
Actual Return

head (TreeNode*)

        │
        ▼
Compilation Error
```

---

## How LeetCode Solves This

Instead of returning a pointer, LeetCode modifies the original tree **in-place**.

Every recursive call directly updates the tree by changing the `left` and `right` pointers.

```
Original Tree

        1
       / \
      2   5

↓

Recursive Calls Modify Pointers

↓

Flattened Tree

1
 \
  2
   \
    3
     \
      4
       \
        5
```

No new head is returned because the original tree itself is transformed into the required linked list.

---

### Key Difference

| GFG | LeetCode |
|------|----------|
| `Node* flatten(Node* root)` | `void flatten(TreeNode* root)` |
| Returns the head of the flattened list | Returns nothing |
| `return head;` is required | `return head;` causes a compilation error |
| Uses the returned pointer to connect subtrees | Directly modifies the tree in-place |
