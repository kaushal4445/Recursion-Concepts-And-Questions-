# Combination Sum — Easy Explanation with Recursion Tree

This README explains the classic **"pick or don't pick"** recursion approach to the
**Combination Sum** problem, using a simple diagram so the logic clicks visually.

---

## 1. The Problem

Given an array `candidates` (numbers can repeat in the answer) and a `target`,
find **all unique combinations** where the chosen numbers add up exactly to `target`.
The **same number can be reused** any number of times.

Example we'll trace throughout this README:
```
candidates = [2, 3]
target     = 5
Answer     = [[2, 3]]
```

---

## 2. The Core Idea — "Pick or Don't Pick"

At every index `i`, for the current candidate `candidates[i]`, we have **two choices**:

| Choice | What happens | Recursive call |
|---|---|---|
| ❌ Don't pick `candidates[i]` | move to next index, target stays same | `solve(i+1, target)` |
| ✅ Pick `candidates[i]` | add it to `temp`, target shrinks, **stay at same `i`** (reuse allowed!) | `solve(i, target - candidates[i])` |

That's exactly what the code does:

```cpp
solve(candidates, target, i + 1, temp);              // don't pick
temp.push_back(candidates[i]);
solve(candidates, target - candidates[i], i, temp);   // pick (stay at i, reuse allowed)
temp.pop_back();                                      // backtrack
```

### Base cases (when to stop)
- `target < 0` → went too far, this path is invalid → **return**
- `target == 0` → found a valid combination → **save `temp` into result**
- `i == candidates.size()` → no more candidates left to try → **return**

---

## 3. Recursion Tree (visual)

Let's trace `candidates = [2, 3]`, `target = 5`. Each box is one recursive
call, shown as `(i, target)`. From every box the code branches two ways —
**skip** the current candidate (left) or **pick** it and stay at the same
index so it can be reused (right).

<p align="center">

<svg width="680" height="480" viewBox="0 0 680 480" xmlns="http://www.w3.org/2000/svg" font-family="'Segoe UI', Helvetica, Arial, sans-serif" role="img" aria-label="Recursion tree for combinationSum([2,3], target=5)">
  <title>Recursion tree for combinationSum([2,3], target=5)</title>
  <rect x="0" y="0" width="680" height="480" fill="#ffffff"/>

  <defs>
    <marker id="arrow" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="#888780" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
  </defs>

  <!-- edges -->
  <line x1="315" y1="96" x2="145" y2="150" stroke="#888780" stroke-width="1" marker-end="url(#arrow)"/>
  <line x1="315" y1="96" x2="480" y2="150" stroke="#888780" stroke-width="1" marker-end="url(#arrow)"/>
  <line x1="145" y1="206" x2="145" y2="260" stroke="#888780" stroke-width="1" marker-end="url(#arrow)"/>
  <line x1="480" y1="206" x2="400" y2="260" stroke="#888780" stroke-width="1" marker-end="url(#arrow)"/>
  <line x1="480" y1="206" x2="560" y2="260" stroke="#888780" stroke-width="1" marker-end="url(#arrow)"/>
  <line x1="400" y1="316" x2="310" y2="370" stroke="#888780" stroke-width="1" marker-end="url(#arrow)"/>
  <line x1="400" y1="316" x2="495" y2="370" stroke="#888780" stroke-width="1" marker-end="url(#arrow)"/>

  <text x="185" y="118" text-anchor="middle" font-size="12" fill="#5F5E5A">skip 2</text>
  <text x="440" y="118" text-anchor="middle" font-size="12" fill="#5F5E5A">pick 2</text>

  <!-- root -->
  <rect x="230" y="40" width="170" height="56" rx="8" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.5"/>
  <text x="315" y="63" text-anchor="middle" font-size="14" font-weight="600" fill="#2C2C2A">i=0, target=5</text>
  <text x="315" y="81" text-anchor="middle" font-size="12" fill="#444441">start</text>

  <!-- L1a: skip 2 -->
  <rect x="60" y="150" width="170" height="56" rx="8" fill="#F1EFE8" stroke="#5F5E5A" stroke-width="0.5"/>
  <text x="145" y="173" text-anchor="middle" font-size="14" font-weight="600" fill="#2C2C2A">i=1, target=5</text>
  <text x="145" y="191" text-anchor="middle" font-size="12" fill="#444441">skip candidates[0]</text>

  <!-- L1b: pick 2 (on path) -->
  <rect x="395" y="150" width="170" height="56" rx="8" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.5"/>
  <text x="480" y="173" text-anchor="middle" font-size="14" font-weight="600" fill="#04342C">i=0, target=3</text>
  <text x="480" y="191" text-anchor="middle" font-size="12" fill="#085041">pick 2, temp=[2]</text>

  <!-- Dead A -->
  <rect x="60" y="260" width="170" height="56" rx="8" fill="#FCEBEB" stroke="#A32D2D" stroke-width="0.5"/>
  <text x="145" y="283" text-anchor="middle" font-size="14" font-weight="600" fill="#501313">no solution</text>
  <text x="145" y="301" text-anchor="middle" font-size="12" fill="#791F1F">all paths dead end</text>

  <!-- L2a: skip 2 again (on path) -->
  <rect x="330" y="260" width="140" height="56" rx="8" fill="#E1F5EE" stroke="#0F6E56" stroke-width="0.5"/>
  <text x="400" y="283" text-anchor="middle" font-size="14" font-weight="600" fill="#04342C">i=1, target=3</text>
  <text x="400" y="301" text-anchor="middle" font-size="12" fill="#085041">skip 2 again</text>

  <!-- L2b: pick 2 again (dead end) -->
  <rect x="490" y="260" width="140" height="56" rx="8" fill="#FCEBEB" stroke="#A32D2D" stroke-width="0.5"/>
  <text x="560" y="283" text-anchor="middle" font-size="14" font-weight="600" fill="#501313">i=0, target=1</text>
  <text x="560" y="301" text-anchor="middle" font-size="12" fill="#791F1F">no solution</text>

  <!-- L3a: skip 3 (dead) -->
  <rect x="230" y="370" width="160" height="56" rx="8" fill="#FCEBEB" stroke="#A32D2D" stroke-width="0.5"/>
  <text x="310" y="393" text-anchor="middle" font-size="14" font-weight="600" fill="#501313">i=2, target=3</text>
  <text x="310" y="411" text-anchor="middle" font-size="12" fill="#791F1F">no candidates left</text>

  <!-- L3b: pick 3 (FOUND) -->
  <rect x="410" y="370" width="170" height="56" rx="8" fill="#EAF3DE" stroke="#3B6D11" stroke-width="0.5"/>
  <text x="495" y="393" text-anchor="middle" font-size="14" font-weight="600" fill="#173404">target=0</text>
  <text x="495" y="411" text-anchor="middle" font-size="12" fill="#27500A">found: [2, 3]</text>

  <!-- legend -->
  <rect x="60" y="450" width="12" height="12" rx="3" fill="#0F6E56"/>
  <text x="78" y="460" font-size="12" fill="#444441">on solution path</text>
  <rect x="220" y="450" width="12" height="12" rx="3" fill="#3B6D11"/>
  <text x="238" y="460" font-size="12" fill="#444441">found</text>
  <rect x="310" y="450" width="12" height="12" rx="3" fill="#A32D2D"/>
  <text x="328" y="460" font-size="12" fill="#444441">dead end</text>
</svg>

</p>

- 🟢 **green** — the leaf where `target == 0`, the combination gets saved
- 🔵 **teal** — boxes on the winning path (`pick 2` → `skip 2` → `pick 3`)
- 🔴 **red** — dead ends, killed by either `target < 0` or running out of candidates
- ⚪ **gray** — the root, and the branch that never leads anywhere

Only **one leaf** ever hits `target == 0` — the branch where we
**pick 2, then skip 2 again, then pick 3**, giving `temp = [2, 3]`.
Every other leaf dies from either `target < 0` (overshot) or
`i == candidates.size()` (ran out of candidates). The full, uncollapsed
version of every branch is in the step-by-step trace below.

---

## 4. Clean Step-by-Step Trace (exact numbers)

`candidates = [2, 3]`, `target = 5`

```
solve(i=0, target=5, temp=[])
│
├── DON'T PICK 2 → solve(i=1, target=5, temp=[])
│   │
│   ├── DON'T PICK 3 → solve(i=2, target=5, temp=[])
│   │       i == size(2)  → STOP (return)
│   │
│   └── PICK 3 → temp=[3] → solve(i=1, target=2, temp=[3])
│       │
│       ├── DON'T PICK 3 → solve(i=2, target=2, temp=[3])
│       │       i == size → STOP
│       │
│       └── PICK 3 → temp=[3,3] → solve(i=1, target=-1, temp=[3,3])
│               target < 0 → STOP
│       (backtrack: temp back to [3], then to [])
│
└── PICK 2 → temp=[2] → solve(i=0, target=3, temp=[2])
    │
    ├── DON'T PICK 2 → solve(i=1, target=3, temp=[2])
    │   │
    │   ├── DON'T PICK 3 → solve(i=2, target=3, temp=[2])
    │   │       i == size → STOP
    │   │
    │   └── PICK 3 → temp=[2,3] → solve(i=1, target=0, temp=[2,3])
    │           target == 0 → ✅ SAVE [2, 3]
    │       (backtrack: temp back to [2,3]→pop→[2])
    │
    └── PICK 2 → temp=[2,2] → solve(i=0, target=1, temp=[2,2])
        │
        ├── DON'T PICK 2 → solve(i=1, target=1, temp=[2,2])
        │   │
        │   ├── DON'T PICK 3 → solve(i=2, target=1, temp=[2,2])
        │   │       i == size → STOP
        │   │
        │   └── PICK 3 → temp=[2,2,3] → solve(i=1, target=-2, temp=[2,2,3])
        │           target < 0 → STOP
        │       (backtrack: pop → [2,2])
        │
        └── PICK 2 → temp=[2,2,2] → solve(i=0, target=-1, temp=[2,2,2])
                target < 0 → STOP
            (backtrack: pop all → temp=[])
```

### ✅ Final Result
```
result = [[2, 3]]
```

Only **one** valid combination exists for `target = 5` with `[2, 3]`
(`2 + 3 = 5`). Note `[3, 2]` is **not** generated separately — because we
always move forward through indices (`i` only increases or stays the same,
never decreases), we naturally avoid duplicate orderings like `[3,2]`.

---

## 5. Why `i` (not `i+1`) When We Pick?

This is the trick that allows **reusing the same number multiple times**:

```cpp
temp.push_back(candidates[i]);
solve(candidates, target - candidates[i], i, temp);   // stays at i!
```

If we instead called `solve(i + 1, ...)` after picking, each number could be
used **only once** (that would solve a different problem: *Combination Sum II*).

Staying at `i` says: *"you can pick `candidates[i]` again in the future."*

---

## 6. Why Backtracking (`push_back` + `pop_back`) Matters

`temp` is a **single shared vector** reused across all recursive branches
(passed by reference `&temp`). So:

1. Before exploring the "pick" branch → `push_back(candidates[i])`
2. After that branch fully finishes exploring → `pop_back()`

This removes the last picked number so the **next branch** (e.g., a sibling
"don't pick" path) doesn't see a leftover value from a previous, unrelated path.

```
temp = []
 → push 2 → temp = [2]        (exploring this branch...)
     → push 3 → temp = [2, 3]  → target==0 → SAVE [2,3]
     → pop     → temp = [2]    (undo, so we can try other options)
 → pop     → temp = []         (back to start, clean slate)
```

Think of it like **exploring a maze with a pencil**: draw a line as you walk
forward (push), and erase it when you back up to try a different path (pop) —
so your map stays accurate for every new path you try.

---

## 7. Complexity

| | |
|---|---|
| **Time** | Exponential in the worst case — roughly `O(2^target)` branches, since at each state we either skip or pick, bounded by how many times target can be reduced. |
| **Space** | `O(target / min(candidates))` for recursion depth + result storage. |

---

## 8. One-Line Mental Model

> **"At each candidate, either skip it forever, or take it and stay right
> here in case you want it again — then undo your choice once you're done
> exploring that path."**
