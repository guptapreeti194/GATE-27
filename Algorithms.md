# GATE 2027 – Algorithms: Complete Revision Notes

Sorting, searching, hashing, heaps and basic graph definitions are in the Data Structures notes; this document covers the algorithmic side and the design techniques. Read once, then practise PYQs.

---

## 1. Asymptotic Analysis

- Growth order: `1 < log log n < log n < √n < n < n log n < n² < n³ < 2ⁿ < n! < nⁿ`. `(log n)^k` is smaller than `n^ε`; `n^k` is smaller than `cⁿ` for c > 1; `log(n!) = Θ(n log n)`.
- f = O(g): f ≤ c·g eventually. Ω: lower bound. Θ: both. o / ω: strict versions.
- Properties: transitive (O, Ω, Θ); Θ is symmetric; f = O(g) ⇔ g = Ω(f). `2^(n+1) = O(2ⁿ)` but `2^(2n) ≠ O(2ⁿ)`. `n^(log n)` is super-polynomial but sub-exponential. `log(n^k) = Θ(log n)`, but `log_a n` vs `log_b n` differ only by a constant, while `a^n` vs `b^n` do not.
- Loop analysis: halving/doubling → log n; `i = i*i` → log log n; nested dependent loops → sum the series.
- **Space complexity**: count extra memory including recursion stack (depth × frame).
- **Amortized analysis**: aggregate, accounting, potential. Examples: dynamic array doubling (O(1) amortized push), binary counter increment (O(1) amortized), union-find with path compression, stack with multipop.

### Recurrences

- **Master theorem**: T(n) = aT(n/b) + f(n), a ≥ 1, b > 1. Compare f(n) with n^(log_b a):
  - f = O(n^(log_b a − ε)) → Θ(n^(log_b a))
  - f = Θ(n^(log_b a)) → Θ(n^(log_b a) log n); if f = Θ(n^c log^k n) → Θ(n^c log^(k+1) n)
  - f = Ω(n^(log_b a + ε)) and regularity → Θ(f(n))
- Master theorem **does not apply** when f is not polynomially different (e.g. n/log n), a is not constant, or the subproblem sizes are unequal.
- Standard results:

| Recurrence | Result |
| --- | --- |
| T(n) = T(n−1) + 1 | n |
| T(n) = T(n−1) + n | n² |
| T(n) = T(n−1) + log n | n log n |
| T(n) = 2T(n−1) + 1 | 2ⁿ |
| T(n) = T(n/2) + 1 | log n |
| T(n) = T(n/2) + n | n |
| T(n) = 2T(n/2) + n | n log n |
| T(n) = 2T(n/2) + 1 | n |
| T(n) = 4T(n/2) + n | n² |
| T(n) = 7T(n/2) + n² | n^2.81 (Strassen) |
| T(n) = 3T(n/2) + n | n^1.585 (Karatsuba) |
| T(n) = T(√n) + 1 | log log n |
| T(n) = 2T(√n) + log n | log n · log log n |
| T(n) = T(n/3) + T(2n/3) + n | n log n |
| T(n) = T(n/5) + T(7n/10) + n | n |
| T(n) = T(n−1) + T(n−2) | \~1.618ⁿ (exponential) |

- Recursion tree method for unequal splits: total = (per-level cost) × (number of levels); the longest branch decides the depth.

---

## 2. Divide and Conquer

Divide → conquer recursively → combine.

| Problem | Time | Note |
| --- | --- | --- |
| Binary search | O(log n) | T(n) = T(n/2) + 1 |
| Merge sort | Θ(n log n) | extra space n |
| Quick sort | avg n log n, worst n² | see DS notes |
| Finding max and min together | ⌈3n/2⌉ − 2 comparisons | D&C gives the same count for n a power of 2 |
| Second largest | n + ⌈log₂ n⌉ − 2 comparisons | tournament method |
| Median / k-th smallest (selection) | O(n) worst with median-of-medians; O(n) expected with randomised quickselect (worst O(n²)) | groups of 5: T(n) = T(n/5) + T(7n/10) + O(n) |
| Strassen matrix multiplication | O(n^2.81) | 7 multiplications instead of 8 per 2×2 block |
| Karatsuba multiplication | O(n^1.585) | 3 multiplications |
| Closest pair of points | O(n log n) | strip check needs only 7 (or 6) neighbours |
| Convex hull (D&C/Graham scan/Andrew) | O(n log n) | Jarvis march O(nh) |
| Counting inversions (merge-sort based) | O(n log n) |  |
| Maximum subarray (D&C) | O(n log n); Kadane O(n) |  |
| Fast exponentiation | O(log n) multiplications |  |
| Tower of Hanoi | 2ⁿ − 1 moves |  |

- Sorting lower bound (comparison model): Ω(n log n). Searching in sorted array lower bound: Ω(log n).

---

## 3. Greedy Method

Make the locally best choice at each step. Needs **greedy-choice property** and **optimal substructure**. Proof by exchange argument or "greedy stays ahead".

- **Activity selection / interval scheduling**: sort by **finish time**, pick compatible. O(n log n). (Sorting by start time or duration is wrong.)
- **Fractional knapsack**: sort by value/weight ratio. O(n log n). (0/1 knapsack is NOT greedy-solvable.)
- **Job sequencing with deadlines and profits**: sort by profit descending, place each job in the latest free slot ≤ its deadline. O(n²) simple, O(n log n) with union-find.
- **Huffman coding**: repeatedly merge the two smallest frequencies. O(n log n) with min-heap. n symbols → n−1 merges, 2n−1 nodes; prefix-free; total bits = Σ freq × depth = sum of all internal-node weights. Code for a symbol is never a prefix of another. Fixed-length code needs ⌈log₂ n⌉ bits.
- **Optimal merge pattern**: merge the two smallest files each time (same as Huffman).
- **Minimum spanning tree** (Kruskal, Prim) and **Dijkstra** are greedy.
- **Minimum number of platforms / room scheduling**: sort events, sweep.
- **Coin change**: greedy works only for canonical coin systems (e.g. 1, 5, 10, 25); fails for e.g. {1, 3, 4} with 6 → greedy 4+1+1, optimal 3+3.
- Greedy vs DP: if the choice depends on future subproblem results → DP; if a single best local choice is provably safe → greedy.

---

## 4. Dynamic Programming

Use when subproblems overlap and optimal substructure holds. **Memoization** (top-down, only needed states) vs **tabulation** (bottom-up). Time = (number of states) × (work per state). Space can often be reduced by keeping only the previous row.

| Problem | Recurrence idea | Time | Space |
| --- | --- | --- | --- |
| Fibonacci | F(n)=F(n−1)+F(n−2) | O(n) | O(1) |
| **0/1 Knapsack** | K(i,w)=max(K(i−1,w), v_i+K(i−1,w−w_i)) | O(nW) – **pseudo-polynomial** | O(W) |
| **LCS** | match: 1+L(i−1,j−1); else max(L(i−1,j), L(i,j−1)) | O(mn) | O(min(m,n)) for length |
| **LIS** | L(i)=1+max L(j), j\<i, a_j\<a_i | O(n²); O(n log n) with patience sorting |  |
| **Edit distance** | min of insert, delete, replace | O(mn) |  |
| **Matrix chain multiplication** | M(i,j)=min_k M(i,k)+M(k+1,j)+p\_{i−1}p_kp_j | O(n³) time, O(n²) space |  |
| **Coin change (min coins / ways)** | C(v)=min C(v−c)+1 | O(nV) |  |
| **Rod cutting** | R(n)=max p_i+R(n−i) | O(n²) |  |
| **Subset sum / partition** | S(i,s) | O(n·sum) pseudo-poly |  |
| **Optimal BST** | min over root choice | O(n³) (O(n²) with Knuth) |  |
| **Floyd–Warshall** | d_k(i,j)=min(d\_{k−1}(i,j), d\_{k−1}(i,k)+d\_{k−1}(k,j)) | O(V³) | O(V²) |
| **Bellman–Ford** | relax all edges V−1 times | O(VE) |  |
| **TSP (Held–Karp)** | DP over subsets | O(n²·2ⁿ) | O(n·2ⁿ) |
| Longest palindromic subsequence | = LCS(s, reverse s) | O(n²) |  |
| Longest common substring | DP with reset | O(mn) |  |
| Number of ways in grid / Catalan | simple DP | O(mn) |  |
| Weighted interval scheduling | sort + binary search + DP | O(n log n) |  |
| Longest path in a DAG | DP in topological order | O(V+E) |  |

- **Number of subproblems** questions: matrix chain → n(n−1)/2 ≈ O(n²) subproblems; LCS → (m+1)(n+1).
- Matrix chain: multiplying a p×q by q×r matrix costs p·q·r scalar multiplications. Parenthesization count = Catalan(n−1). Practise filling the table for 4–5 matrices by hand.
- LCS tracing: LCS length for two strings with no common characters = 0; LCS(X, X) = |X|.
- Knapsack is NP-hard, yet O(nW) is **not** polynomial in input size (W is a number, takes log W bits).
- Longest **simple** path in a general graph is NP-hard (DP/greedy won't work), but shortest path is polynomial.

---

## 5. Graph Algorithms

### Traversal

- **BFS**: queue; O(V+E); shortest path in edges for unweighted graphs; detects bipartiteness; levels. **DFS**: stack/recursion; O(V+E); discovery/finish times; edge types (tree, back, forward, cross). Undirected DFS → only tree and back edges.
- **Parenthesis theorem**: for any u, v, intervals \[d\[u\], f\[u\]\] and \[d\[v\], f\[v\]\] are either disjoint or nested. **White-path theorem**: v becomes a descendant of u iff at time d\[u\] there is an all-white path u → v.
- Graph is acyclic ⇔ DFS finds no back edge.

### Topological sort and SCC

- Topological sort (DAG): order by decreasing finish time, or Kahn's algorithm. O(V+E). Unique iff consecutive vertices are connected by an edge.
- **SCC**: Kosaraju (two DFS, second on transpose in decreasing finish time) or Tarjan. O(V+E). The component graph is a DAG.
- **Articulation point / bridge**: DFS with low-link values. Root is an articulation point iff it has ≥ 2 DFS children. O(V+E).

### Minimum Spanning Tree

- Cut property: lightest edge crossing any cut belongs to some MST. Cycle property: heaviest edge in a cycle is not in any MST (if unique).
- **Kruskal**: sort edges + union-find → O(E log E) = O(E log V); good for sparse graphs. **Prim**: grow from one vertex; binary heap O(E log V); adjacency matrix + array O(V²); Fibonacci heap O(E + V log V); good for dense graphs.
- MST is **unique** if all edge weights are distinct. If weights are not distinct, the MST weight is still unique, but the tree may not be. Minimum spanning tree also minimises the **bottleneck** (max edge) path between any two vertices; it does **not** guarantee shortest paths between vertices.
- Adding a constant to all edge weights or squaring (positive) weights keeps the same MST; shortest paths can change.
- Second-best MST: O(V²) or better. Number of edges in MST = V−1.
- Reverse-delete and Borůvka are other MST algorithms (Borůvka O(E log V)).

### Single-source shortest paths

| Algorithm | Condition | Time |
| --- | --- | --- |
| BFS | unweighted | O(V+E) |
| **Dijkstra** | non-negative weights | O((V+E) log V) binary heap; O(V²) array; O(E + V log V) Fibonacci |
| **Bellman–Ford** | negative edges OK; detects negative cycle reachable from source | O(VE) |
| DAG relax in topological order | DAG, any weights | O(V+E) |

- Dijkstra fails with negative edges (even without negative cycles). Adding a constant to every edge does **not** preserve shortest paths (paths with more edges are penalised).
- Dijkstra's invariant: when a vertex is extracted, its distance is final. Running time with a sorted-array or unsorted-array PQ → O(V²) / O(V² + E).
- Bellman–Ford: after i iterations, correct for shortest paths using ≤ i edges. A V-th iteration that still relaxes ⇒ negative cycle.
- Shortest path is NOT necessarily unique; a shortest-path tree has V−1 edges.

### All-pairs shortest paths

- **Floyd–Warshall** O(V³), handles negative edges (not negative cycles); also transitive closure (Warshall). Matrix multiplication (min,+) approach O(V³ log V).
- **Johnson's algorithm**: reweight using Bellman–Ford, then Dijkstra from every vertex → O(V² log V + VE); good for sparse graphs.
- Running Dijkstra V times: O(V(V+E) log V).

### Network flow (occasionally asked)

- Max-flow min-cut theorem. **Ford–Fulkerson** O(E · |f\*|) with integer capacities; **Edmonds–Karp** (BFS augmenting path) O(VE²). Bipartite matching via max-flow. Flow value = capacity of min cut.

---

## 6. Sorting, Searching, Hashing (summary – details in DS notes)

- Comparison sort lower bound Ω(n log n); counting/radix/bucket can beat it with assumptions.
- Stable: merge, insertion, bubble, counting, radix. Unstable: quick, heap, selection.
- Insertion sort running time = Θ(n + inversions). Quick sort worst case n² on sorted input with first/last pivot. Heap sort O(n log n) always. Merge sort extra space O(n).
- Selection of k-th smallest: O(n) via median of medians; heap-based O(n + k log n).
- Binary search: ⌊log₂ n⌋ + 1 comparisons worst case. Interpolation search O(log log n) average for uniform keys.
- Hashing: average O(1), worst O(n); chaining successful search ≈ 1 + α/2; open addressing expected probes ≈ 1/(1−α) for unsuccessful.

---

## 7. Backtracking and Branch & Bound

- **Backtracking**: DFS on the state space tree with pruning. Examples: N-Queens, graph colouring, Hamiltonian cycle, subset sum, sudoku. Worst case exponential. N-Queens solutions: n=4 → 2, n=6 → 4, n=8 → 92.
- **Branch and bound**: BFS/best-first with bounding functions; used in 0/1 knapsack, TSP, job assignment.
- Subset-sum / graph colouring (m colours) state-space tree has O(2ⁿ) / O(mⁿ) nodes.

---

## 8. Complexity Classes: P, NP, NP-Complete

- **P**: decidable in polynomial time. **NP**: verifiable in polynomial time (certificate) / solvable by nondeterministic TM in polynomial time. P ⊆ NP. **P = NP is open.**
- **NP-hard**: every NP problem reduces to it in polynomial time (need not be in NP). **NP-complete** = NP ∩ NP-hard.
- To prove L is NP-complete: (1) L ∈ NP, (2) reduce a **known** NPC problem to L (direction matters: known → new).
- If any NPC problem is in P, then P = NP. If A ≤ₚ B and B ∈ P then A ∈ P. If A ≤ₚ B and A is NP-hard then B is NP-hard.
- **Cook–Levin theorem**: SAT is NP-complete (first NPC problem). Chain: SAT → 3-SAT → Clique / Independent Set → Vertex Cover → Hamiltonian cycle → TSP (decision); Subset Sum → Partition / Knapsack (decision).

| In P | NP-Complete |
| --- | --- |
| 2-SAT, Horn-SAT | 3-SAT, SAT, CNF-SAT (k ≥ 3) |
| 2-colourability (bipartite) | 3-colourability, k-colouring (k ≥ 3) |
| Shortest path, MST, matching (bipartite and general), max flow | Clique, Vertex Cover, Independent Set, Set Cover |
| Euler circuit/path | Hamiltonian cycle/path, TSP (decision) |
| Primality (AKS), linear programming | Subset sum, Partition, 0/1 knapsack (decision), Bin packing |
| Sorting, searching, shortest path w/o negative cycles | Longest simple path, Steiner tree, Graph isomorphism (unknown, not known NPC), Integer programming |

- Complementary relations: Clique ⇔ Independent Set in complement graph; Vertex Cover: S is a vertex cover iff V∖S is an independent set.
- **Approximation**: Vertex cover 2-approximation (take both endpoints of a maximal matching); metric TSP 2-approx (MST doubling), 1.5-approx (Christofides); general TSP has no constant-factor approximation unless P=NP; Set cover O(ln n) greedy.
- Co-NP: complement of NP. Tautology is co-NP-complete. NP ∩ co-NP contains primes (now P). If NP ≠ co-NP then P ≠ NP.
- Decidability (TOC) vs complexity: don't confuse.

---

## 9. Quick Reference: Time Complexity

| Algorithm | Time |
| --- | --- |
| Merge sort / Heap sort | Θ(n log n) |
| Quick sort (worst) | O(n²) |
| Counting sort | O(n + k) |
| Binary search | O(log n) |
| BFS / DFS | O(V + E) |
| Topological sort / SCC | O(V + E) |
| Kruskal | O(E log V) |
| Prim (heap / array) | O(E log V) / O(V²) |
| Dijkstra (heap / array) | O(E log V) / O(V²) |
| Bellman–Ford | O(VE) |
| Floyd–Warshall | O(V³) |
| Johnson | O(V² log V + VE) |
| Matrix chain / Optimal BST | O(n³) |
| LCS / Edit distance / 0-1 Knapsack | O(mn) / O(mn) / O(nW) |
| Held–Karp TSP | O(n² 2ⁿ) |
| Strassen / Karatsuba | O(n^2.81) / O(n^1.585) |
| Huffman | O(n log n) |
| Median of medians | O(n) |

---

## 10. High-Yield Traps and Shortcuts

1. **Recurrence questions**: first check if the master theorem applies; if not, use the recursion tree. Watch for a non-polynomial gap (n log n vs n^(log_b a) logs — use the extended case).
2. `T(n) = 2T(n/2) + n/log n` → Θ(n log log n) (master theorem fails).
3. Greedy fails for 0/1 knapsack and general coin change; works for fractional knapsack, activity selection (by finish time), Huffman.
4. Kruskal vs Prim: sparse → Kruskal; dense with matrix → Prim O(V²). The MST is not the shortest-path tree.
5. Dijkstra with negative edge → wrong; with a negative edge and **no** negative cycle, Bellman–Ford is right. Negative cycle → shortest path undefined.
6. Floyd–Warshall with negative cycle: diagonal entry becomes negative.
7. DP table questions: fill carefully by hand; most marks come from not slipping on indices. For LCS, remember to take max of up and left when characters differ.
8. Matrix chain: cost of (A×B)×C vs A×(B×C); pick the dimension array correctly (n matrices → n+1 dimensions).
9. NP-completeness: knowing which problems are in P (2-SAT, 2-colouring, Euler, shortest path) is tested often; "Longest path", "Hamiltonian", "3-colouring", "Vertex cover" → NPC.
10. Pseudo-polynomial ≠ polynomial (knapsack, subset sum, partition).
11. BFS shortest paths work only for unweighted (or equal-weight) graphs; BFS tree has no forward edges in undirected graphs; in directed graphs BFS has tree, back, cross edges.
12. Topological ordering count: DAG with no edges on n vertices → n! orderings; unique ordering ⇔ Hamiltonian path exists.
13. Number of spanning trees of K_n = n^(n−2); MST of a graph with n vertices has n−1 edges; disconnected graph has a spanning forest.
14. Time complexity of "number of distinct substrings", "palindromic partition", "egg dropping" → check state count × transition cost.
15. Amortised vs average vs worst case — different concepts; amortised is a worst-case guarantee over a sequence.
16. Compare functions by taking logs: `n^(log n)` vs `2^(√n)`, `(log n)^(log n)` vs `n^(log log n)`; take log of both sides, simplify.
17. For "minimum/maximum number of comparisons" questions, use the known results: max/min ⌈3n/2⌉−2; second largest n+⌈log n⌉−2; sorting 5 elements needs 7.
18. Strassen only improves asymptotics for large n; naive is Θ(n³).

---

## 11. Practice Plan

1. **Topic-wise PYQs** (GATE CSE 2010–2026): start with recurrences and asymptotics (easy marks), then greedy/DP table-filling, then graph algorithms (MST, shortest path, DFS edge types), then NP-completeness theory statements.
2. Hand-execute: Dijkstra, Bellman–Ford, Kruskal, Prim, Huffman tree, LCS/knapsack/matrix-chain tables, Floyd–Warshall on 4 vertices, DFS with discovery/finish times.
3. Keep an **error log** of concept + trap; revise only it in the final two weeks.
4. Be able to write pseudo-code for: merge, partition, BFS, DFS, Dijkstra, Kruskal with union-find, LCS, knapsack. Code-tracing questions become easy once you can write these.
5. Memorise the complexity table in section 9 and the NPC / P list in section 8.
6. Take full-length mocks; Algorithms plus DS usually carry a solid share of the paper, and most questions are solvable with the facts above.
