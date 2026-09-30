# GATE 2027 – Data Structures: Complete Revision Notes

Read once properly, revise the **bold** items and tables, then spend all remaining time on PYQs.

---

## 1. Complexity & Recurrences

- Order: `1 < log n < √n < n < n log n < n² < n³ < 2ⁿ < n! < nⁿ`
- Big-O = upper bound, Ω = lower bound, Θ = tight. Best/worst/average are **cases**, O/Ω/Θ are **bounds**; they are independent.
- Loop `for(i=1;i<n;i*=2)` → log n. `for(i=n;i>0;i/=2)` → log n. Nested `i` to n, inner `j` to i → n²/2 = Θ(n²).
- `for(i=2;i<n;i=i*i)` → log log n.
- **Master theorem** T(n)=aT(n/b)+f(n), compare f(n) with n^(log_b a):
  - f smaller (polynomially) → Θ(n^(log_b a))
  - f equal → Θ(n^(log_b a) · log n)
  - f larger (+ regularity) → Θ(f(n))
- Common: T(n)=T(n-1)+n → n²; T(n)=T(n-1)+1 → n; T(n)=2T(n-1)+1 → 2ⁿ; T(n)=T(n/2)+1 → log n; T(n)=2T(n/2)+n → n log n; T(n)=T(√n)+1 → log log n; T(n)=T(n/3)+T(2n/3)+n → n log n.
- Fibonacci recursion (no memo) = exponential (\~1.618ⁿ).

---

## 2. Arrays

- **Row-major** address of A\[i\]\[j\] (lower bounds L1, L2; N2 columns; size w): `B + [(i−L1)·N2 + (j−L2)]·w`
- **Column-major**: `B + [(j−L2)·N1 + (i−L1)]·w` (N1 = number of rows)
- 3-D (row-major), dimensions d1×d2×d3: `B + (i·d2·d3 + j·d3 + k)·w`
- Lower triangular (n×n) stored row-wise, 1-indexed: A\[i\]\[j\] at position `i(i−1)/2 + j`. Total elements n(n+1)/2.
- Tri-diagonal: 3n−2 elements. Symmetric: n(n+1)/2.
- Access O(1), search unsorted O(n), sorted+binary O(log n), insert/delete in middle O(n).
- Sparse matrix: triplet (row, col, val) form saves space when nonzeros \< n²/3 (roughly).
- In C: `a[i]` ≡ `*(a+i)` ≡ `i[a]`. Pointer arithmetic scales by element size. 2-D array name decays to pointer to a row.

---

## 3. Linked Lists

| Operation | Singly | Doubly | Circular singly (tail ptr) |
| --- | --- | --- | --- |
| Insert at head | O(1) | O(1) | O(1) |
| Insert at tail | O(n) (O(1) with tail ptr) | O(1) with tail | O(1) |
| Delete head | O(1) | O(1) | O(1) |
| Delete tail | O(n) | O(1) with tail | O(n) |
| Delete given node ptr | O(n) (O(1) trick: copy next's data, skip next; fails for last node) | O(1) | O(n) |
| Search | O(n) | O(n) | O(n) |

- **Delete last node in O(1)** needs doubly linked with tail pointer. **Insert at both ends O(1) + delete at both ends O(1)** → doubly circular / doubly with head & tail.
- Reverse a list: 3 pointers (prev, curr, next), O(n) time, O(1) space.
- **Floyd cycle detection**: slow (+1), fast (+2); meet → cycle. Find middle: fast reaches end when slow is at middle.
- Merge two sorted lists O(m+n). Merge sort is the best sort for linked lists (no random access needed). Binary search on linked list is NOT O(log n).
- XOR linked list: stores `prev XOR next`, one pointer per node.
- Polynomial addition, big-number arithmetic are classic applications.
- Memory: array needs contiguous block; list has pointer overhead but dynamic size.

---

## 4. Stack

- LIFO. push/pop/peek O(1). Overflow (full), underflow (empty).
- **Applications**: function calls/recursion, expression evaluation, parenthesis matching, DFS, undo, infix→postfix.
- **Infix → Postfix**: operand → output; operator → pop while stack top has higher or equal precedence (for left-associative); `^` is right-associative (pop only if strictly higher); `(` push; `)` pop until `(`.
- **Postfix evaluation**: operand push; operator pops b first then a, computes `a op b`, push.
- Precedence: `^` > `* /` > `+ -`.
- Prefix = evaluate right to left.
- Max stack size needed to convert an expression = number of operators+parentheses nested at peak (count carefully in questions).
- **Stack permutations**: number of distinct permutations of 1..n obtainable using a stack = Catalan number `C(2n,n)/(n+1)`. Permutation is impossible if pattern "3 1 2" (i.e., i\<j\<k output as k, i, j) appears.
- **Two stacks in one array**: grow from both ends; overflow when top1+1 == top2.
- **Queue using two stacks**: enqueue O(1), dequeue amortized O(1) (worst O(n)); or make enqueue costly.
- **Stack using two queues**: one operation costs O(n).
- Min-stack: extra stack to get min in O(1).
- Recursion depth = stack frames; uncontrolled recursion → stack overflow.

---

## 5. Queue

- FIFO. Enqueue at rear, dequeue at front, O(1).
- **Linear array queue** wastes space; **circular queue** fixes it.
  - Next: `(i+1) % n`
  - Empty: `front == rear` (when one slot is sacrificed)
  - Full: `(rear+1) % n == front` → holds at most n−1 elements
  - Without sacrificing a slot, use a count variable.
  - Number of elements = `(rear − front + n) % n`
- **Deque**: insert/delete at both ends; input-restricted / output-restricted variants.
- **Priority queue**: best implemented with heap (insert O(log n), extract O(log n)); unsorted array (insert O(1), extract O(n)); sorted array (insert O(n), extract O(1)).
- Applications: BFS, scheduling, buffering, level-order traversal.
- Queue with linked list: keep both front and rear pointers for O(1) both ops. With a single pointer in a circular list pointing to rear, both O(1).

---

## 6. Trees – Basics

- n nodes → n−1 edges. Level of root = 0 (or 1 – read the question!). Height of single node = 0 (GATE usually), of empty tree = −1.
- **Binary tree**: max nodes at level i = 2ⁱ; max nodes in height h = 2^(h+1) − 1; min height for n nodes = ⌈log₂(n+1)⌉ − 1 = ⌊log₂ n⌋; max height = n−1 (skewed).
- **n₀ = n₂ + 1** (leaves = degree-2 nodes + 1). Independent of degree-1 nodes.
- k-ary tree with i internal nodes: nodes = k·i + 1, leaves = (k−1)·i + 1. For full k-ary tree.
- **Full (strict)** binary tree: 0 or 2 children; n = 2·leaves − 1. **Complete**: all levels full except last, filled left to right. **Perfect**: all leaves same level.
- **Counting**: unlabeled binary trees with n nodes = Catalan `C(2n,n)/(n+1)`; labeled = n! × Catalan. Number of distinct BSTs on n keys = Catalan.
- **Array representation** (1-indexed): left = 2i, right = 2i+1, parent = ⌊i/2⌋. (0-indexed: 2i+1, 2i+2, ⌊(i−1)/2⌋.) Skewed tree wastes exponential space.
- Left-child right-sibling converts any tree to a binary tree.
- **Threaded binary tree**: null pointers replaced by inorder predecessor/successor links. A binary tree with n nodes has n+1 null pointers.

### Traversals

- Inorder (L N R), Preorder (N L R), Postorder (L R N), Level-order (queue). All DFS ones O(n), recursion space O(h).
- **Unique reconstruction**: Inorder + Preorder, or Inorder + Postorder. Preorder + Postorder alone is NOT unique (unique only for full binary trees). Preorder+level-order etc. also possible but rare.
- Inorder of BST → sorted ascending. Reverse inorder → descending.
- Preorder of BST alone determines the BST; count of BSTs with given preorder = 1.
- Root of a tree: first in preorder, last in postorder.
- Number of binary trees with given preorder sequence of n nodes = Catalan(n).
- Height from traversal trick: number of binary trees where preorder = inorder means only left-empty (right-skewed).

---

## 7. Binary Search Tree (BST)

- Left \< node \< right (for distinct keys).
- Search/insert/delete: O(h). Average (random inserts) h = O(log n); worst (sorted insertion) h = n−1 → O(n).
- **Delete**: leaf → remove; one child → replace with child; two children → replace with inorder successor (min of right subtree) or predecessor (max of left), then delete that node.
- Min = leftmost, Max = rightmost. Successor: if right subtree exists → its min, else the lowest ancestor whose left subtree contains the node.
- Finding the k-th smallest: inorder, O(h + k), or O(log n) with size augmentation.
- Checking BST: inorder sorted or pass (min,max) bounds — not just comparing node with children.
- Same set of keys in different insertion orders → different shapes; same shape possible from different orders.
- To build a BST from sorted array with minimal height: choose the middle as root recursively.
- Searching sequence validity: on path of search for key K, keys visited must narrow the (low, high) interval monotonically — typical GATE question ("which cannot be the sequence of nodes examined").
- Inserting n keys into empty BST: O(n log n) average, O(n²) worst.

---

## 8. AVL Tree

- BST with |height(left) − height(right)| ≤ 1 for every node (balance factor ∈ {−1, 0, 1}).
- **Rotations**: LL → right rotation; RR → left rotation; LR → left then right; RL → right then left.
- Search/insert/delete all **O(log n)** worst case.
- **Insertion**: at most one (single or double) rotation restores balance. **Deletion**: may need O(log n) rotations.
- **Minimum nodes** for height h: `N(h) = N(h−1) + N(h−2) + 1`, with N(0)=1, N(1)=2, N(2)=4, N(3)=7, N(4)=12, N(5)=20, N(6)=33. (Height of single node = 0.)
- **Maximum height** with n nodes ≈ 1.44 log₂ n.
- Maximum nodes for height h = 2^(h+1) − 1 (perfect tree).
- Inorder traversal still sorted.

### Red-Black tree (know basics)

- Root black, leaves (NIL) black, red node has black children, every root-to-leaf path has same number of black nodes.
- Height ≤ 2 log₂(n+1). Insert/delete O(log n), at most 2 rotations on insertion, 3 on deletion.
- Faster insert/delete than AVL (fewer rotations), AVL is more strictly balanced (faster lookup).

---

## 9. Heap

- **Complete binary tree** stored in an array. Max-heap: parent ≥ children; min-heap: parent ≤ children.
- **Insert**: place at end, heapify-up → O(log n). **Delete-max/extract**: swap root with last, heapify-down → O(log n).
- **Build-heap** (bottom-up, Floyd): **O(n)**. Building by n successive inserts: O(n log n).
- Heapify-down starts from index ⌊n/2⌋ down to 1. **Leaves are at indices ⌊n/2⌋+1 … n**. Number of leaves = ⌈n/2⌉.
- Height = ⌊log₂ n⌋. Number of nodes at height h ≤ ⌈n / 2^(h+1)⌉.
- **Searching** an arbitrary element: O(n). Finding min in a max-heap: O(n) (it's in a leaf). Find max in max-heap: O(1).
- **Heap sort**: build O(n) + n extractions O(n log n) = O(n log n) worst, in-place, NOT stable.
- Delete arbitrary element (given index): O(log n). Decrease/increase key: O(log n).
- Merging two heaps: O(n) by rebuild (binary heap); binomial/Fibonacci heaps do better (Fibonacci: insert O(1) amortized, decrease-key O(1) amortized).
- k largest from n elements: min-heap of size k → O(n log k).
- Min number of comparisons to build a heap of n elements ≤ 2n.
- A sorted array is a valid min-heap; a reverse-sorted array is a valid max-heap.
- Height of a heap with n=2^k elements: k.

---

## 10. B-Tree / B+ Tree (usually asked with DBMS, but it is a data structure)

- **B-tree of order m**: every node ≤ m children (≤ m−1 keys); root ≥ 2 children (if not leaf); other internal nodes ≥ ⌈m/2⌉ children (≥ ⌈m/2⌉−1 keys); all leaves at same level.
- Height bound for n keys: `h ≤ log_⌈m/2⌉ ((n+1)/2)`.
- Insert: overflow → split, median goes up; tree grows at root. Delete: underflow → borrow from sibling or merge.
- **B+ tree**: all data in leaves, leaves linked (fast range scan), internal nodes only keys (higher fan-out, shorter tree). Order definitions differ (order of internal nodes p vs leaf node pleaf) — read the question's definition.
- Node size ≈ disk block size. Max keys per node for block B, key size k, pointer size p: solve `(m−1)·k + m·p ≤ B`.
- Search/insert/delete: O(log n) disk accesses.

---

## 11. Hashing

- Hash function maps keys → table index. Good one: uniform distribution, cheap. Common: `h(k)=k mod m` (m prime preferred), multiplication, universal hashing.
- **Load factor** α = n/m.

### Collision resolution

- **Chaining (separate)**: list per slot. Average successful search ≈ 1 + α/2; unsuccessful ≈ α (plus 1 to hash). Worst-case search O(n). α can exceed 1. Deletion is easy.
- **Open addressing** (α ≤ 1): every entry in the table.
  - Linear probing: `(h(k)+i) mod m` → **primary clustering**.
  - Quadratic probing: `(h(k)+c₁i+c₂i²) mod m` → **secondary clustering**; may not probe all slots.
  - Double hashing: `(h₁(k)+i·h₂(k)) mod m` → best distribution, avoids both clustering types; h₂(k) must be nonzero and relatively prime to m.
  - Expected probes (uniform hashing): unsuccessful ≤ 1/(1−α); successful ≤ (1/α)·ln(1/(1−α)).
  - Linear probing: unsuccessful ≈ ½(1 + 1/(1−α)²), successful ≈ ½(1 + 1/(1−α)).
  - **Deletion** needs a tombstone/"deleted" marker, otherwise later searches break.
- Number of keys that can be stored in open addressing ≤ m.
- Expected number of collisions when n keys hashed uniformly into m slots: `n − m + m(1−1/m)ⁿ`; expected pairs colliding = C(n,2)/m.
- Perfect hashing gives O(1) worst-case for static sets.
- Average O(1) search/insert/delete; worst O(n). Hash tables don't support ordered operations efficiently.
- **Questions to practice**: insert a sequence into table of size 10 with `h(k)=k mod 10` using linear/quadratic probing and find the final positions or which slot a key lands in.

---

## 12. Graphs

- Undirected max edges = n(n−1)/2; directed (no self loops) = n(n−1). Sum of degrees = 2E. In directed: Σ in-degree = Σ out-degree = E.
- **Connected** graph needs ≥ n−1 edges; a graph with > (n−1)(n−2)/2 edges is necessarily connected; **Tree** = connected + acyclic (n−1 edges).
- Number of labeled spanning trees of K_n = n^(n−2) (Cayley). Number of graphs on n labeled vertices = 2^(n(n−1)/2).
- Number of components = n − (edges in spanning forest).

### Representation

|  | Adjacency matrix | Adjacency list |
| --- | --- | --- |
| Space | O(V²) | O(V+E) |
| Check edge (u,v) | O(1) | O(deg) |
| List neighbors | O(V) | O(deg) |
| Best for | dense graphs | sparse graphs |

- Incidence matrix: V×E. Undirected adjacency matrix is symmetric. Number of walks of length k from i to j = (A^k)\[i\]\[j\].

### Traversals

- **BFS** (queue): O(V+E) list, O(V²) matrix; gives shortest path in unweighted graphs; level order.
- **DFS** (stack/recursion): O(V+E); discovery/finish times; classifies edges: tree, back, forward, cross. Back edge ⇔ cycle (directed or undirected). Undirected DFS has only tree and back edges.
- **Topological sort** (DAG only): via DFS finishing time (decreasing order) or Kahn's (in-degree). O(V+E). A DAG can have multiple topological orders; unique iff there is a Hamiltonian path in it.
- **Strongly connected components**: Kosaraju/Tarjan O(V+E).
- Articulation point: removing disconnects graph; bridge: edge whose removal disconnects. Tarjan low-link O(V+E).
- Bipartite ⇔ no odd cycle ⇔ 2-colorable (BFS check).
- Cycle detection undirected: DFS/Union-Find.

### MST and shortest path (quick facts; full treatment is in Algorithms)

- **Kruskal**: sort edges, Union-Find, O(E log E) = O(E log V). **Prim**: O(E log V) with binary heap, O(V²) with array, O(E + V log V) with Fibonacci heap.
- MST unique if all edge weights distinct. Cut property, cycle property. Max-weight edge in a cycle is never in MST (if unique).
- **Dijkstra**: no negative edges, O(E log V) heap / O(V²) array. **Bellman-Ford**: O(VE), negative edges, detects negative cycles. **Floyd-Warshall**: O(V³). DAG shortest path: O(V+E).

---

## 13. Disjoint Set (Union-Find)

- Operations: make-set, find, union.
- **Union by rank/size + path compression** → amortized α(n) (inverse Ackermann, practically constant) per operation.
- With only union by rank: O(log n). Naive linked-list implementation: union O(n).
- Height of tree with union by rank ≤ log₂ n.
- Used in Kruskal, connected components, cycle detection.

---

## 14. Sorting (frequently asked, high weightage)

| Algorithm | Best | Average | Worst | Space | Stable | In-place |
| --- | --- | --- | --- | --- | --- | --- |
| Bubble (with flag) | n | n² | n² | 1 | Yes | Yes |
| Selection | n² | n² | n² | 1 | No | Yes |
| Insertion | n | n² | n² | 1 | Yes | Yes |
| Merge | n log n | n log n | n log n | n | Yes | No |
| Quick | n log n | n log n | n² | log n (stack) | No | Yes |
| Heap | n log n | n log n | n log n | 1 | No | Yes |
| Counting | n+k | n+k | n+k | n+k | Yes | No |
| Radix | d(n+k) | d(n+k) | d(n+k) | n+k | Yes | No |
| Bucket | n | n | n² | n | Yes (if stable inner) | No |

- **Comparison sorts** have lower bound Ω(n log n) (decision tree with n! leaves: height ≥ log₂(n!) ≈ n log n). Min comparisons to sort n elements ≥ ⌈log₂ n!⌉ (sorting 4 elements → 5, 5 elements → 7).
- **Insertion sort**: number of swaps/shifts = number of **inversions**. Max inversions = n(n−1)/2 (reverse sorted). Good for small or nearly-sorted input, online. Binary insertion sort reduces comparisons to O(n log n) but shifts remain O(n²).
- **Selection sort**: exactly n(n−1)/2 comparisons always, at most n−1 swaps.
- **Bubble sort**: worst n(n−1)/2 comparisons and swaps.
- **Quick sort**: partition O(n). Worst case when input is already sorted/reverse sorted (first/last pivot) or all equal (Lomuto) → T(n)=T(n−1)+n. Best/pivot at median → T(n)=2T(n/2)+n. Split always 9:1 still O(n log n). Randomized pivot → expected O(n log n). Hoare vs Lomuto partition differ in swap counts.
- **Merge sort**: T(n)=2T(n/2)+n; comparisons between n/2·log n and n log n − n + 1; merging two sorted arrays of sizes m, n needs at most m+n−1 comparisons.
- **Heap sort**: see Section 9.
- **Counting sort**: keys in range 0..k; works when k = O(n). Radix sort uses a stable sort per digit, from least significant digit.
- Stability matters when sorting on multiple keys; quick sort and heap sort can be made stable only with extra space/tweaks.
- Internal vs external: merge sort is the basis of external sorting. k-way merge with heap: O(n log k).
- Minimum number of comparisons to find **max and min** together = ⌈3n/2⌉ − 2. Second largest = n + ⌈log₂ n⌉ − 2. Find max = n−1. Median via selection: O(n) worst (median of medians).
- **Searching**: linear O(n); binary search O(log n) needs sorted array with random access; comparisons worst = ⌊log₂ n⌋ + 1; interpolation average O(log log n) for uniform data.
- Merging k sorted lists of total n elements: O(n log k).

---

## 15. Quick Reference: Operation Complexity

| Structure | Access | Search | Insert | Delete |
| --- | --- | --- | --- | --- |
| Array (unsorted) | O(1) | O(n) | O(1) at end / O(n) | O(n) |
| Array (sorted) | O(1) | O(log n) | O(n) | O(n) |
| Linked list | O(n) | O(n) | O(1) at head | O(1) given ptr (doubly) |
| Stack / Queue | – | O(n) | O(1) | O(1) |
| BST avg / worst | – | log n / n | log n / n | log n / n |
| AVL / Red-Black | – | log n | log n | log n |
| Binary heap | max O(1) | O(n) | O(log n) | O(log n) |
| Hash table avg / worst | – | 1 / n | 1 / n | 1 / n |
| B-tree | – | log n | log n | log n |

---

## 16. High-Yield Traps and Shortcuts (read before every mock)

1. **Height convention**: check if the question counts root at level/height 0 or 1. GATE typically: single node has height 0, but read the statement.
2. n₀ = n₂ + 1 — use for "number of leaves" questions, degree-1 nodes irrelevant.
3. Heap: **build is O(n)**, but sorting with it is O(n log n). Finding min in max-heap is O(n).
4. Circular queue full/empty condition differ by implementation — read how many slots are used.
5. Postfix evaluation: second popped operand is the **left** operand (a − b, where b popped first).
6. Hashing: check table size, probe sequence formula, and whether question asks **position** or **number of probes**. Re-check after each insert.
7. Deleting from open-addressing needs tombstones.
8. AVL min-nodes recurrence N(h)=N(h−1)+N(h−2)+1; note N(0)=1 when height of one node is 0.
9. BST "possible search sequence" questions: check that every visited node lies within the interval formed by earlier comparisons.
10. Preorder+postorder does not uniquely give a tree.
11. Quick sort worst case occurs for sorted input with first/last element as pivot; merge sort never degrades.
12. Insertion sort is best for nearly sorted data; number of swaps equals inversions.
13. DFS back edge ⇔ cycle; topological sort only on DAG.
14. Adjacency list BFS/DFS = O(V+E); matrix = O(V²).
15. Floyd cycle detection, reverse list, merge lists — be able to **trace pointer code** quickly. Always draw the list and execute step by step; watch for NULL dereference on the last node.
16. C pointer/recursion output questions: trace with a stack table of (function call, local variables, return value). Watch for static/global variables, pass by value vs pointer, and operator precedence (`*p++`, `++*p`).
17. Number of BSTs/binary trees/stack permutations/valid parenthesizations/triangulations → all Catalan numbers: 1, 1, 2, 5, 14, 42, 132, 429.
18. Amortized analysis: dynamic array doubling → O(1) amortized push; incrementing a binary counter → O(1) amortized per increment; two-stack queue → O(1) amortized.

---

## 17. Practice Plan (what remains for you)

1. **PYQs topic-wise** (GATE CSE 2010–2026 + GATE DA sets): do trees/heap/hashing/sorting first, then linked list/stack/queue tracing, then graphs.
2. Maintain an **error log**: for every wrong answer write the concept and the trap; revise only the log in the last two weeks.
3. Hand-simulate: hash insertions, BST insert/delete, AVL rotations, heap build, quicksort partition, tree reconstruction from traversals, infix→postfix.
4. Write (on paper) codes for: list reversal, cycle detection, BST insert/delete/traversals, heapify, merge, partition, BFS/DFS. Being able to write them makes code-tracing questions easy.
5. Solve NAT-type questions without calculator dependence: memorize Catalan numbers, AVL min-node table, and log values.
6. Take full-length mocks; DS usually contributes \~6–10 marks combined with Programming and Algorithms, so accuracy here is the cheapest marks in the paper.
