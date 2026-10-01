# GATE 2027 – Engineering Mathematics: Complete Revision Notes

Mapped to the official GATE 2027 (IIT Madras) CS syllabus you uploaded:

- **Discrete Mathematics:** propositional and first-order logic; sets, relations, functions, partial orders and lattices; monoids, groups; graphs (connectivity, matching, colouring); combinatorics (counting, recurrence relations, generating functions)
- **Linear Algebra:** matrices, determinants, linear systems, eigenvalues and eigenvectors, LU decomposition
- **Calculus:** limits, continuity, differentiability, maxima and minima, mean value theorem, integration
- **Probability and Statistics:** random variables; uniform, normal, exponential, Poisson, binomial distributions; mean, median, mode, SD; conditional probability and Bayes' theorem

Read once, memorise the bold items, then practise PYQs. Engineering Maths is about 13 to 15 marks, mostly scoring.

---

# PART A – DISCRETE MATHEMATICS

## A1. Propositional Logic

- Connectives: ¬, ∧, ∨, →, ↔, ⊕. `p → q` is false only when p = T, q = F.
- **Equivalences**: `p → q ≡ ¬p ∨ q ≡ ¬q → ¬p` (contrapositive). Converse `q → p` and inverse `¬p → ¬q` are **not** equivalent to the original. `p ↔ q ≡ (p→q) ∧ (q→p) ≡ ¬(p ⊕ q)`.
- De Morgan: `¬(p∧q) ≡ ¬p∨¬q`, `¬(p∨q) ≡ ¬p∧¬q`. Negation of `p → q` is `p ∧ ¬q`. Absorption: `p ∨ (p∧q) ≡ p`. Distributive, idempotent, double negation.
- **Tautology** (always T), **contradiction** (always F), **contingency**, **satisfiable** (some assignment gives T). Valid argument ⇔ (premises → conclusion) is a tautology.
- Number of Boolean functions of n variables = 2^(2ⁿ). Number of rows in a truth table = 2ⁿ.
- **Functionally complete sets**: {∧,¬}, {∨,¬}, {→,¬}, **{NAND}**, **{NOR}**. {∧,∨} and {→} alone are not complete.
- **Inference rules**: modus ponens (p, p→q ⊢ q); modus tollens (¬q, p→q ⊢ ¬p); hypothetical syllogism (p→q, q→r ⊢ p→r); disjunctive syllogism (p∨q, ¬p ⊢ q); resolution (p∨q, ¬p∨r ⊢ q∨r). **Fallacies**: affirming the consequent (q, p→q ⊢ p), denying the antecedent.
- CNF / DNF; a CNF is valid iff every clause contains a complementary pair.

## A2. First-Order Logic

- Quantifiers ∀, ∃. `¬∀x P ≡ ∃x ¬P`, `¬∃x P ≡ ∀x ¬P`.
- **Translation rules**: "All A are B" → `∀x (A(x) → B(x))`. "Some A are B" → `∃x (A(x) ∧ B(x))`. Using ∧ with ∀ or → with ∃ is the usual wrong answer.
- Order matters: `∀x∃y P(x,y)` (for each x there is a y) is **not** equivalent to `∃y∀x P(x,y)`; the second implies the first.
- **Distribution**: ∀ distributes over ∧: `∀x(P∧Q) ≡ ∀xP ∧ ∀xQ`. ∃ distributes over ∨: `∃x(P∨Q) ≡ ∃xP ∨ ∃xQ`. Only one direction holds for `∀xP ∨ ∀xQ → ∀x(P∨Q)` and `∃x(P∧Q) → ∃xP ∧ ∃xQ`.
- Free vs bound variables; a sentence has no free variables. An FOL statement is true or false only under an interpretation (domain + predicate meanings).
- Evaluate "∀x∃y (x \< y)" over ℕ (true) vs over a finite set (false for the maximum).

## A3. Sets, Relations, Functions

### Sets

- `|A ∪ B| = |A| + |B| − |A ∩ B|`; three sets by inclusion–exclusion. Power set has 2ⁿ elements; |A×B| = |A||B|. Number of subsets of size k = C(n,k).
- De Morgan for sets, symmetric difference `A Δ B`.

### Relations on a set with n elements

| Type | Count |
| --- | --- |
| All relations | 2^(n²) |
| Reflexive | 2^(n²−n) |
| Irreflexive | 2^(n²−n) |
| Symmetric | 2^(n(n+1)/2) |
| Antisymmetric | 2ⁿ · 3^(n(n−1)/2) |
| Asymmetric | 3^(n(n−1)/2) |
| Reflexive and symmetric | 2^(n(n−1)/2) |
| Symmetric and antisymmetric | 2ⁿ |
| **Equivalence relations** | Bell number: 1, 1, 2, 5, 15, 52, 203 (n = 0..6) |

- **Equivalence relation** = reflexive + symmetric + transitive; classes partition the set; number of classes = rank of the partition. Intersection of equivalence relations is an equivalence relation; union generally is not.
- **Partial order** = reflexive + antisymmetric + transitive. Poset. Total order = partial order where all pairs are comparable.
- Relation composition `R∘S`; matrix form uses Boolean product. **Transitive closure** via Warshall in O(n³). Properties through matrix: symmetric ⇔ M = Mᵀ.
- Number of antisymmetric/partial orders on small sets (n = 3: 19 partial orders).

### Functions

- f: A → B with |A| = m, |B| = n. Total functions: nᵐ. **One-one** (m ≤ n): n!/(n−m)! = P(n,m). **Onto**: Σ\_{k=0}^{n} (−1)^k C(n,k)(n−k)ᵐ = n!·S(m,n) (S = Stirling number of the second kind). **Bijections** (m = n): n!.
- Composition: g∘f one-one ⇒ f one-one; g∘f onto ⇒ g onto. Inverse exists iff bijective. Composition of bijections is a bijection.
- Pigeonhole: n items in k boxes → some box has ≥ ⌈n/k⌉. Number of elements needed to guarantee r in the same box: k(r−1) + 1.
- Floor/ceiling: ⌈x⌉ = −⌊−x⌋, ⌊n/2⌋ + ⌈n/2⌉ = n.

## A4. Partial Orders and Lattices

- **Hasse diagram**: draw cover relations, no loops/transitive edges, upward direction.
- **Maximal/minimal** (no larger/smaller element); **greatest/least** (comparable to all, unique if they exist). Upper bound, lower bound, **lub (join, ∨)**, **glb (meet, ∧)**.
- **Chain** = totally ordered subset; **antichain** = pairwise incomparable. **Dilworth**: minimum number of chains covering a poset = size of the largest antichain.
- **Lattice**: every pair has lub and glb. Every finite lattice is bounded (has 0 and 1). Every chain is a lattice (and distributive). Laws: idempotent, commutative, associative, absorption.
- **Distributive lattice**: `a∧(b∨c) = (a∧b)∨(a∧c)`. A lattice is distributive iff it has no sublattice isomorphic to **N5** (pentagon) or **M3** (diamond). In a distributive lattice a complement is unique if it exists.
- **Complemented lattice**: every element has a complement (`a∨a' = 1`, `a∧a' = 0`). **Boolean lattice** = complemented distributive lattice; it has 2ⁿ elements, isomorphic to the power set of an n-set; atoms count = n.
- Divisibility lattice **(Dₙ, |)**: always a distributive lattice; it is Boolean iff n is **square-free**. The poset (P(S), ⊆) is a Boolean lattice. (ℤ⁺, |) is a lattice with lub = LCM, glb = GCD.
- Number of lattices on n elements: n = 1..5: 1, 1, 1, 2, 5.
- A poset where every non-empty subset has a lub and glb is a complete lattice.

## A5. Algebraic Structures

- **Closure → Semigroup (+ associativity) → Monoid (+ identity) → Group (+ inverses) → Abelian group (+ commutativity)**.
- Group facts: identity and inverses are unique; cancellation holds; `(ab)⁻¹ = b⁻¹a⁻¹`; if every element satisfies a² = e, the group is abelian.
- **Examples**: (ℤ, +) group; (ℕ, +) monoid only; (ℤₙ, +ₙ) group; (ℤₙ\*, ×ₙ) group of units (elements with gcd(a,n)=1), order φ(n); (ℤₚ\*, ×) cyclic for prime p; Sₙ symmetric group, order n!; Klein four-group (non-cyclic, abelian); matrices under multiplication (non-abelian, if invertible).
- **Subgroup test**: non-empty and `a,b ∈ H ⇒ ab⁻¹ ∈ H` (or closure + inverses for finite). Intersection of subgroups is a subgroup; union is a subgroup iff one contains the other.
- **Lagrange**: |H| divides |G|; order of any element divides |G|; group of prime order is cyclic and has no non-trivial subgroups. Index = |G|/|H|.
- **Cyclic groups**: order n has φ(n) generators; one subgroup for each divisor of n; number of elements of order d = φ(d); order of element `a` in ℤₙ is n/gcd(a,n). Every cyclic group is abelian; every subgroup of a cyclic group is cyclic.
- **Order of group elements**: o(aᵏ) = o(a)/gcd(o(a),k).
- Groups of order 4: two (ℤ₄, Klein V₄). Order 6: ℤ₆ and S₃. Order ≤ 5: all abelian.
- Homomorphism f(ab) = f(a)f(b); kernel is a normal subgroup; first isomorphism theorem; Cayley's theorem (every group embeds in a symmetric group). **Normal subgroup**: gHg⁻¹ = H; index-2 subgroups are normal.
- Rings/fields: ℤₙ is a field iff n prime. Group tables are Latin squares. Number of subgroups of ℤₙ = number of divisors of n.

## A6. Graph Theory

### Basics

- **Handshaking lemma**: Σ deg = 2E, so the number of odd-degree vertices is even. Directed: Σ in = Σ out = E.
- Max edges: simple undirected n(n−1)/2; directed n(n−1). k-regular graph has nk/2 edges. Complement edges = n(n−1)/2 − E.
- Degree sequence validity: even sum, check with Havel–Hakimi.
- **Tree** facts: connected + acyclic, n−1 edges, unique path between any two vertices, at least 2 leaves (n ≥ 2); a forest with c components has n−c edges. Labelled trees on n vertices = n^(n−2) (Cayley). Number of spanning trees of Kₙ = n^(n−2); Kirchhoff's matrix tree theorem gives the count for general graphs.

### Connectivity

- With n vertices and c components: n − c ≤ E ≤ (n−c)(n−c+1)/2. A simple graph with E > (n−1)(n−2)/2 is connected.
- **Cut vertex (articulation point)**, **bridge**; vertex connectivity κ ≤ edge connectivity λ ≤ min degree δ.
- A simple graph with δ ≥ (n−1)/2 is connected.

### Euler and Hamilton

- **Euler circuit**: connected and all degrees even. **Euler path**: exactly 0 or 2 odd vertices. Directed: in-degree = out-degree for all vertices (and connected).
- **Hamiltonian cycle**: Dirac (δ ≥ n/2, n ≥ 3), Ore (deg u + deg v ≥ n for non-adjacent pairs) are sufficient, not necessary. Kₙ has (n−1)!/2 distinct Hamiltonian cycles. K\_{m,n} Hamiltonian iff m = n ≥ 2. Kₙ has an Euler circuit iff n is odd.

### Planar graphs

- **Euler's formula**: V − E + F = 2 (connected); V − E + F = 1 + c in general.
- Simple planar graph, V ≥ 3: **E ≤ 3V − 6**; if no triangles (e.g. bipartite): **E ≤ 2V − 4**. Sum of face degrees = 2E. Every planar graph has a vertex of degree ≤ 5.
- K₅ and K₃,₃ are non-planar; **Kuratowski/Wagner**: a graph is planar iff it has no subdivision of K₅ or K₃,₃. Number of faces in a planar graph with E edges, V vertices: F = E − V + 2.

### Colouring

- **χ(G)** = minimum colours for a proper vertex colouring. Bipartite ⇔ χ ≤ 2 ⇔ no odd cycle; tree χ = 2; Kₙ: n; cycle Cₙ: 2 (n even), 3 (n odd); wheel (hub + rim cycle): 3 if the rim has an even number of vertices, 4 if odd; χ ≤ Δ + 1; **Brooks**: χ ≤ Δ unless complete graph or odd cycle. **Four colour theorem**: planar → χ ≤ 4. A graph with a clique of size k has χ ≥ k. χ(G) ≥ n/α(G).
- **Edge colouring** (chromatic index χ′): Vizing: Δ ≤ χ′ ≤ Δ+1. For Kₙ: n−1 if n even, n if n odd. Bipartite graphs: χ′ = Δ. Chromatic polynomial of a tree on n vertices: k(k−1)^(n−1); of Kₙ: k(k−1)…(k−n+1); of Cₙ: (k−1)ⁿ + (−1)ⁿ(k−1).

### Matching

- **Matching**: set of edges with no common vertex. **Perfect matching**: covers all vertices (needs even n). K\_{m,n} max matching = min(m,n); K\_{2n} has (2n−1)!! = (2n)!/(2ⁿ n!) perfect matchings.
- **Hall's theorem**: bipartite graph (X, Y) has a matching saturating X iff |N(S)| ≥ |S| for every S ⊆ X. k-regular bipartite graphs (k ≥ 1) have a perfect matching.
- **König's theorem** (bipartite): maximum matching size = minimum vertex cover size. **Gallai**: α(G) + β(G) = n (independent set + vertex cover); matching number + edge cover number = n (no isolated vertices).
- **Tutte's theorem** (general graphs, perfect matching condition). A tree has at most one perfect matching.
- Clique of G = independent set of complement; α(G) = ω(complement). Graph isomorphism invariants: same V, E, degree sequence, cycles, connectivity (necessary, not sufficient).

---

## A7. Combinatorics

### Counting basics

- Sum rule, product rule. Permutations P(n,r) = n!/(n−r)!; combinations C(n,r) = n!/(r!(n−r)!).
- **Circular arrangements**: (n−1)!; if reflection is the same (necklace/bracelet), (n−1)!/2.
- **Repetition**: arrangements with identical objects n!/(n₁! n₂! …). Selecting r objects with repetition from n types: C(n+r−1, r). Selecting r from n with order and repetition: nʳ.
- **Non-negative integer solutions** of x₁+…+x_k = n: **C(n+k−1, k−1)**. Positive solutions: C(n−1, k−1). With upper bounds → inclusion–exclusion.
- Paths in an m×n grid (right/up moves): C(m+n, m). Paths not crossing the diagonal: Catalan.
- **Binomial theorem**: (x+y)ⁿ = Σ C(n,k) xᵏ yⁿ⁻ᵏ. ΣC(n,k) = 2ⁿ, Σ(−1)ᵏC(n,k) = 0, ΣkC(n,k) = n2ⁿ⁻¹, Pascal's rule C(n,k) = C(n−1,k−1) + C(n−1,k), Vandermonde ΣC(m,i)C(n,k−i) = C(m+n,k).
- **Inclusion–exclusion**: |A₁∪…∪Aₙ| = Σ|Aᵢ| − Σ|Aᵢ∩Aⱼ| + …
- **Derangements**: Dₙ = n!Σ(−1)ᵏ/k! = (n−1)(Dₙ₋₁ + Dₙ₋₂); D₁..D₆ = 0, 1, 2, 9, 44, 265. Probability that a random permutation is a derangement → 1/e. Permutations with exactly r fixed points: C(n,r)·Dₙ₋ᵣ.
- **Catalan numbers** Cₙ = C(2n,n)/(n+1): 1, 1, 2, 5, 14, 42, 132 (balanced parentheses, binary trees, stack permutations, triangulations of (n+2)-gon, non-crossing partitions, Dyck paths).
- **Stirling numbers** of second kind S(n,k) (partitions of n into k non-empty sets): S(n,2) = 2ⁿ⁻¹ − 1; Bell number = Σ S(n,k). Integer partitions p(n) (unordered sums).
- Distribute n identical balls in k distinct boxes: C(n+k−1,k−1); distinct balls into distinct boxes: kⁿ.

### Recurrence relations

- **Linear homogeneous, constant coefficients**: aₙ = c₁aₙ₋₁ + … + c_kaₙ₋ₖ. Characteristic equation rᵏ = c₁rᵏ⁻¹ + … + c_k.
  - Distinct roots: aₙ = Σ αᵢ rᵢⁿ.
  - Repeated root r of multiplicity m: (α₀ + α₁n + … + α\_{m−1}n^(m−1)) rⁿ.
  - Complex roots a ± bi → rⁿ(α cos nθ + β sin nθ).
- **Non-homogeneous**: aₙ = homogeneous solution + particular solution. For RHS of the form c·sⁿ: try A·sⁿ; if s is a root of multiplicity m, try A·nᵐ·sⁿ. For polynomial RHS of degree d try a polynomial of degree d (times n if 1 is a root).
- Classic: aₙ = aₙ₋₁ + aₙ₋₂ (Fibonacci), closed form with φ = (1+√5)/2 (Binet); aₙ = 2aₙ₋₁ + 1, a₀ = 0 → 2ⁿ − 1 (Hanoi); aₙ = 2aₙ₋₁ with a₀ = 1 → 2ⁿ; aₙ = aₙ₋₁ + n → n(n+1)/2 + a₀.
- Number of binary strings of length n with no two consecutive 0s: Fibonacci Fₙ₊₂. Number of ways to tile a 1×n strip with 1×1 and 1×2 tiles = Fₙ₊₁.

### Generating functions

- Ordinary GF of (aₙ): A(x) = Σ aₙxⁿ. Key series:
  - 1/(1−x) = Σxⁿ (all aₙ = 1); 1/(1−ax) = Σaⁿxⁿ.
  - 1/(1−x)² = Σ(n+1)xⁿ; x/(1−x)² = Σ n xⁿ.
  - **1/(1−x)ᵏ = Σ C(n+k−1, n) xⁿ**.
  - (1+x)ⁿ = ΣC(n,k)xᵏ; (1−xⁿ⁺¹)/(1−x) = 1 + x + … + xⁿ.
  - eˣ = Σxⁿ/n! (exponential GF); ln(1/(1−x)) = Σxⁿ/n.
- Operations: shift xᵏ A(x); A(x)B(x) → convolution Σaᵢbₙ₋ᵢ (use for "number of ways" with ≥ constraints); A(x)/(1−x) = partial sums; derivative → n·aₙ.
- Counting problems: coefficient of xⁿ in (1+x+x²+…)ᵏ = C(n+k−1,n); in (1+x+…+x^m)ᵏ requires truncation. Solving a recurrence: multiply by xⁿ, sum, solve for A(x), partial fractions, extract coefficients.

---

# PART B – LINEAR ALGEBRA

## B1. Matrices and Determinants

- Types: symmetric (A = Aᵀ), skew-symmetric (A = −Aᵀ, diagonal 0), orthogonal (AAᵀ = I), idempotent (A² = A), nilpotent (Aᵏ = 0), involutory (A² = I), Hermitian, unitary. Every square matrix = symmetric + skew-symmetric.
- **Determinant properties**: det(AB) = det A · det B; det(Aᵀ) = det A; **det(kA) = kⁿ det A**; det(A⁻¹) = 1/det A; swapping two rows changes sign; adding a multiple of one row to another keeps the value; a zero row or two equal/proportional rows → 0; triangular/diagonal matrix → product of diagonal; det(adj A) = (det A)ⁿ⁻¹; det(Aᵏ) = (det A)ᵏ.
- **Inverse**: A⁻¹ = adj(A)/det(A); exists iff det ≠ 0 (non-singular). (AB)⁻¹ = B⁻¹A⁻¹; (AB)ᵀ = BᵀAᵀ. A·adj(A) = det(A)·I.
- Trace: tr(A+B) = tr A + tr B; tr(AB) = tr(BA). 2×2: A⁻¹ = (1/det)\[\[d,−b\],\[−c,a\]\].
- **Rank** = number of linearly independent rows = number of non-zero rows in echelon form; rank ≤ min(m,n); rank(AB) ≤ min(rank A, rank B); rank(A) = rank(Aᵀ); a matrix with all identical entries has rank 1.

## B2. System of Linear Equations Ax = b (n unknowns)

- **Consistent** iff rank(A) = rank(\[A|b\]).
  - rank A = rank \[A|b\] = n → **unique** solution.
  - rank A = rank \[A|b\] \< n → **infinitely many** (n − r free variables).
  - rank A \< rank \[A|b\] → **no solution**.
- **Homogeneous** Ax = 0: always has the trivial solution; has non-trivial solutions iff rank \< n (for a square matrix iff det = 0). Number of linearly independent solutions = n − rank (nullity).
- **Rank–nullity**: rank + nullity = number of columns.
- Cramer's rule xᵢ = det(Aᵢ)/det(A) (needs det ≠ 0). Gaussian elimination O(n³).
- Row operations preserve the solution set; column operations don't.
- Basis, dimension, span, linear independence; vectors in ℝⁿ: more than n vectors are dependent; n vectors independent iff det ≠ 0.

## B3. Eigenvalues and Eigenvectors

- Av = λv, v ≠ 0. Characteristic equation **det(A − λI) = 0**. For 2×2: λ² − (trace)λ + det = 0.
- **Sum of eigenvalues = trace; product of eigenvalues = det**. So A is singular iff 0 is an eigenvalue.
- Eigenvalues of a triangular/diagonal matrix = diagonal entries. A and Aᵀ have the same eigenvalues. A and P⁻¹AP (similar matrices) have the same eigenvalues.
- Transformations: Aᵏ → λᵏ; A⁻¹ → 1/λ; A + cI → λ + c; cA → cλ; p(A) → p(λ); adj A → det A/λ.
- Special matrices: idempotent → λ ∈ {0,1}; nilpotent → all 0; involutory → ±1; orthogonal/unitary → |λ| = 1; **real symmetric** → real eigenvalues, eigenvectors for distinct eigenvalues orthogonal; skew-symmetric (real) → purely imaginary or 0; Hermitian → real.
- **Cayley–Hamilton**: every matrix satisfies its characteristic equation (use to find A⁻¹ or high powers).
- **Diagonalisable** iff it has n linearly independent eigenvectors (distinct eigenvalues ⇒ diagonalisable; repeated eigenvalue needs geometric multiplicity = algebraic multiplicity). Real symmetric matrices are always diagonalisable (orthogonally).
- Rank-1 matrix uvᵀ: one non-zero eigenvalue = vᵀu, others 0. Eigenvectors of distinct eigenvalues are linearly independent. Geometric multiplicity ≤ algebraic multiplicity.
- Eigenvalue interlacing, Gershgorin discs (rare).

## B4. LU Decomposition

- **A = LU**: L lower triangular (unit diagonal in Doolittle), U upper triangular (Crout: unit diagonal on U).
- Exists (without pivoting) if all **leading principal minors are non-zero**; with row interchanges: PA = LU. Unique for non-singular A when L has unit diagonal.
- Use: to solve Ax = b: Ly = b (forward substitution), Ux = y (back substitution). Factorisation costs ≈ (2/3)n³ operations, each solve O(n²) → efficient for many right-hand sides.
- det(A) = product of the diagonal of U (L unit). Cholesky A = LLᵀ for symmetric positive definite matrices.

---

# PART C – CALCULUS

## C1. Limits and Continuity

- **Standard limits** (x → 0): sin x/x = 1; tan x/x = 1; (1−cos x)/x² = 1/2; (eˣ−1)/x = 1; ln(1+x)/x = 1; (aˣ−1)/x = ln a; (1+x)^(1/x) = e; (1+k/x)^x → eᵏ as x → ∞; xˣ → 1 as x → 0⁺; x ln x → 0 as x → 0⁺; sin(1/x) has no limit at 0, but x·sin(1/x) → 0.
- **Indeterminate forms**: 0/0, ∞/∞, 0·∞, ∞−∞, 1^∞, 0⁰, ∞⁰. **L'Hôpital's rule** for 0/0 and ∞/∞: differentiate numerator and denominator separately; take logs for exponential forms.
- **Squeeze theorem**. Growth: ln x ≪ xᵏ ≪ eˣ as x → ∞.
- **Taylor/Maclaurin**: eˣ = 1 + x + x²/2! + …; sin x = x − x³/3! + …; cos x = 1 − x²/2! + …; ln(1+x) = x − x²/2 + x³/3 − …; 1/(1−x) = 1 + x + x² + …; (1+x)ⁿ = 1 + nx + n(n−1)x²/2! + ….
- **Continuity** at a: lim f = f(a) (left = right = value). Types of discontinuity: removable, jump, infinite. Continuous on a closed interval ⇒ bounded, attains max and min, takes every intermediate value (**IVT**).
- **Differentiability**: differentiable ⇒ continuous; converse false (|x| at 0, cube-root at 0 has a vertical tangent). Left derivative = right derivative needed. Check at "corner" points of piecewise functions.
- Derivatives: (xⁿ)′ = nxⁿ⁻¹; (eˣ)′ = eˣ; (aˣ)′ = aˣ ln a; (ln x)′ = 1/x; (sin x)′ = cos x; (cos x)′ = −sin x; (tan x)′ = sec²x; (sin⁻¹x)′ = 1/√(1−x²); (tan⁻¹x)′ = 1/(1+x²). Product, quotient, chain rule; implicit and logarithmic differentiation.

## C2. Mean Value Theorems

- **Rolle's**: f continuous on \[a,b\], differentiable on (a,b), f(a) = f(b) ⇒ ∃c with f′(c) = 0.
- **Lagrange MVT**: ∃c ∈ (a,b): **f′(c) = (f(b) − f(a))/(b − a)**. Consequences: f′ > 0 ⇒ increasing; f′ = 0 ⇒ constant. Inequalities such as |sin a − sin b| ≤ |a − b|.
- Cauchy MVT: f′(c)/g′(c) = (f(b)−f(a))/(g(b)−g(a)). Taylor's theorem with remainder. All need continuity on the closed interval and differentiability on the open interval; check conditions before applying.

## C3. Maxima and Minima

- **Single variable**: stationary points where f′(x) = 0. Second derivative test: f″ \< 0 → local max; f″ > 0 → local min; f″ = 0 → inconclusive (use the higher-order derivative test: first non-zero derivative of even order decides; odd order → inflection). First derivative sign change also works.
- **Global extrema on \[a,b\]**: compare f at critical points and endpoints. Local ≠ global. Endpoints and non-differentiable points (e.g. |x| at 0) can be extrema.
- Point of inflection: f″ changes sign.
- **Two variables**: critical point fₓ = f_y = 0; D = fₓₓf_yy − (fₓy)². **D > 0 and fₓₓ > 0 → local min; D > 0 and fₓₓ \< 0 → local max; D \< 0 → saddle; D = 0 → inconclusive.**
- Constrained optimisation: Lagrange multipliers ∇f = λ∇g. AM–GM: a + b ≥ 2√(ab); for fixed sum the product is maximised when parts are equal.
- Examples: max of x·e^(−x) at x = 1; min of x + 1/x for x > 0 is 2 at x = 1.

## C4. Integration

- **Standard integrals**: ∫xⁿ = xⁿ⁺¹/(n+1) (n ≠ −1); ∫1/x = ln|x|; ∫eˣ = eˣ; ∫aˣ = aˣ/ln a; ∫sin = −cos; ∫cos = sin; ∫sec² = tan; ∫1/(1+x²) = tan⁻¹x; ∫1/√(1−x²) = sin⁻¹x; ∫ln x = x ln x − x; ∫tan x = −ln|cos x|.
- **By parts**: ∫u dv = uv − ∫v du (ILATE). **Substitution**. Partial fractions.
- **Definite integral properties**:
  - ∫ₐᵇ f(x)dx = ∫ₐᵇ f(a+b−x)dx (use to simplify symmetric integrands, e.g. ∫₀^{π/2} sin x/(sin x + cos x) dx = π/4).
  - Symmetric limits: **even** f → 2∫₀ᵃ f; **odd** f → 0.
  - Periodic with period T: ∫₀^{nT} f = n∫₀ᵀ f.
- **Wallis**: ∫₀^{π/2} sinⁿx dx = ∫₀^{π/2} cosⁿx dx = \[(n−1)(n−3)…\]/\[n(n−2)…\] × (π/2 if n even, 1 if n odd).
- **Gamma function**: Γ(n) = ∫₀^∞ xⁿ⁻¹e⁻ˣ dx; Γ(n+1) = nΓ(n); Γ(n) = (n−1)! for integers; **Γ(1/2) = √π**. ∫₀^∞ e^(−x²)dx = √π/2; ∫₀^∞ xⁿe^(−ax)dx = n!/aⁿ⁺¹. Beta: B(m,n) = Γ(m)Γ(n)/Γ(m+n).
- **Improper integrals**: ∫₁^∞ 1/xᵖ dx converges iff p > 1; ∫₀¹ 1/xᵖ dx converges iff p \< 1; ∫ 1/x diverges at both ends. ∫₀^∞ e^(−ax) = 1/a.
- **Leibniz rule**: d/dx ∫\_{a(x)}^{b(x)} f(t)dt = f(b(x))b′(x) − f(a(x))a′(x).
- **Average value** of f on \[a,b\] = (1/(b−a))∫ₐᵇ f. Area between curves = ∫|f − g|. Riemann sum limits: lim (1/n)Σ f(k/n) = ∫₀¹ f(x)dx.
- **Series**: geometric Σarⁿ = a/(1−r) for |r| \< 1; p-series Σ1/nᵖ converges iff p > 1; harmonic series diverges; ratio and root tests; alternating series test; Σ1/n² = π²/6.
- Euler's theorem for homogeneous functions of degree k: x∂f/∂x + y∂f/∂y = kf. Chain rule for partial derivatives.
- Useful sums: Σk = n(n+1)/2; Σk² = n(n+1)(2n+1)/6; Σk³ = \[n(n+1)/2\]²; GP: a(rⁿ−1)/(r−1).

---

# PART D – PROBABILITY AND STATISTICS

## D1. Basic Probability

- Axioms: 0 ≤ P ≤ 1; P(S) = 1; additivity. P(Aᶜ) = 1 − P(A); P(A∪B) = P(A) + P(B) − P(A∩B).
- **Conditional**: P(A|B) = P(A∩B)/P(B). **Multiplication**: P(A∩B) = P(A)P(B|A).
- **Independence**: P(A∩B) = P(A)P(B). **Mutually exclusive** events with positive probabilities are **never independent**. Pairwise independence ≠ mutual independence.
- **Total probability**: P(B) = ΣP(B|Aᵢ)P(Aᵢ) for a partition {Aᵢ}. **Bayes**: P(Aᵢ|B) = P(B|Aᵢ)P(Aᵢ)/ΣP(B|Aⱼ)P(Aⱼ). Always build the tree diagram; posterior ≠ likelihood.
- "At least one" = 1 − P(none). Classic: n coin tosses; two dice (36 outcomes, P(sum = 7) = 1/6); cards (52); balls with/without replacement; Monty Hall (switching wins 2/3); birthday problem (≥ 23 people → > 50%).
- **Boole/Union bound**: P(∪Aᵢ) ≤ ΣP(Aᵢ).

## D2. Random Variables

- **Discrete**: PMF p(x) ≥ 0, Σp = 1. **Continuous**: PDF f(x) ≥ 0, ∫f = 1, P(a ≤ X ≤ b) = ∫ₐᵇ f; P(X = a) = 0. **CDF** F(x) = P(X ≤ x), non-decreasing, F(−∞) = 0, F(∞) = 1; f = F′.
- **Expectation**: E\[X\] = Σxp(x) or ∫xf(x)dx; E\[g(X)\] = Σg(x)p(x). **Linearity**: E\[aX + bY + c\] = aE\[X\] + bE\[Y\] + c, **always**, even if dependent. Non-negative integer X: E\[X\] = ΣP(X ≥ k).
- **Variance**: Var(X) = E\[X²\] − (E\[X\])²; Var(aX + b) = a²Var(X); Var(X + Y) = Var X + Var Y + 2Cov(X,Y); equals the sum if independent. Cov(X,Y) = E\[XY\] − E\[X\]E\[Y\]; **independent ⇒ Cov = 0, converse false**. |correlation| ≤ 1.
- Joint distributions: marginals, conditionals; E\[X\] = E\[E\[X|Y\]\] (tower property); E\[XY\] = E\[X\]E\[Y\] if independent.
- **Inequalities**: Markov P(X ≥ a) ≤ E\[X\]/a (X ≥ 0); **Chebyshev** P(|X − μ| ≥ kσ) ≤ 1/k². Central limit theorem: the mean of many i.i.d. variables is approximately normal.
- **Linearity-of-expectation shortcuts**: expected number of fixed points of a random permutation = 1; expected number of heads in n tosses = n/2; coupon collector (n coupons) = n·Hₙ ≈ n ln n; expected number of tosses to see the first head = 2; expected tosses to get HH = 6, HT = 4; expected value of one fair die = 3.5, variance 35/12; expected max of two dice = 161/36.

## D3. Distributions (memorise this table)

| Distribution | PMF / PDF | Mean | Variance |
| --- | --- | --- | --- |
| Bernoulli(p) | pˣ(1−p)¹⁻ˣ | p | p(1−p) |
| **Binomial(n,p)** | C(n,k)pᵏ(1−p)ⁿ⁻ᵏ | np | np(1−p) |
| Geometric(p) (trials to first success) | (1−p)ᵏ⁻¹p | 1/p | (1−p)/p² |
| **Poisson(λ)** | e⁻λ λᵏ/k! | λ | λ |
| **Uniform(a,b)** continuous | 1/(b−a) | (a+b)/2 | (b−a)²/12 |
| Discrete uniform {1..n} | 1/n | (n+1)/2 | (n²−1)/12 |
| **Exponential(λ)** | λe⁻λˣ (x ≥ 0) | 1/λ | 1/λ² |
| **Normal(μ,σ²)** | (1/σ√2π)e^(−(x−μ)²/2σ²) | μ | σ² |

- **Binomial**: mode ⌊(n+1)p⌋; sum of independent Binomial(n₁,p) and Binomial(n₂,p) is Binomial(n₁+n₂, p). Poisson approximates Binomial for large n, small p with λ = np. Sum of independent Poissons is Poisson(λ₁+λ₂).
- **Exponential**: P(X > x) = e^(−λx); **memoryless**: P(X > s+t | X > s) = P(X > t). Min of independent exponentials is exponential with the sum of rates. The geometric distribution is the discrete memoryless one. Poisson arrivals ⇔ exponential inter-arrival times.
- **Normal**: symmetric, mean = median = mode; standardise Z = (X − μ)/σ; Φ(−z) = 1 − Φ(z); P(|X − μ| \< σ) ≈ 0.68, 2σ ≈ 0.95, 3σ ≈ 0.997. Sum of independent normals is normal (means add, variances add); aX + b \~ N(aμ + b, a²σ²). Normal approximation to binomial for large n.
- **Uniform**: P(c ≤ X ≤ d) = (d − c)/(b − a). Hypergeometric (sampling without replacement): mean n·K/N.
- Mixed continuous questions: find the normalising constant from ∫f = 1, then mean and variance by integration; use the CDF for medians (F(m) = 0.5).

## D4. Descriptive Statistics

- **Mean** (arithmetic) = Σx/n; **median** = middle value of sorted data (average of two middles when n even); **mode** = most frequent value. **Empirical relation (moderately skewed)**: Mode = 3·Median − 2·Mean. Right-skewed: mean > median > mode; left-skewed: opposite; symmetric: all equal.
- **Variance** σ² = Σ(x − μ)²/n = Σx²/n − μ²; sample variance uses n − 1. SD = √variance. **Adding a constant**: mean shifts, SD unchanged. **Multiplying by k**: mean × k, SD × |k|.
- Combined mean of groups: Σnᵢμᵢ/Σnᵢ. Mean of first n natural numbers = (n+1)/2, variance = (n²−1)/12. **Coefficient of variation** = σ/μ × 100%. Mean ≥ geometric mean ≥ harmonic mean for positive numbers. Adding a new element equal to the mean keeps the mean but reduces the variance.
- Percentiles, quartiles, IQR (resistant to outliers; mean is not).

---

# PART E – High-Yield Traps and Shortcuts

1. **Logic**: translate "All…are" with → and "Some…are" with ∧. Negation flips quantifiers. ∀x∃y ≠ ∃y∀x.
2. Relations: remember the count formulas; **antisymmetric 2ⁿ3^(n(n−1)/2)** and **symmetric 2^(n(n+1)/2)** are frequent NAT questions.
3. **Functions**: onto count uses inclusion–exclusion; one-one needs m ≤ n; a function with domain size > codomain size is never one-one.
4. Lattices: a lattice is distributive iff no N5/M3; (Dₙ, |) is Boolean iff n is square-free; a Boolean algebra's size is always a power of 2. Check the **meet/join definition** for each pair; missing lub breaks lattice-ness.
5. Groups: apply Lagrange first; verify closure, identity, inverse, associativity separately; (ℤₙ\*, ×) is a group only for the units; the set of non-zero elements of ℤₙ under ×ₙ is a group iff n is prime.
6. Graph counting: use handshaking; remember E ≤ 3V − 6 and E ≤ 2V − 4 for planar tests; connected graph minimum edges n − 1; Kₙ Euler circuit needs odd n; the number of Hamiltonian cycles of Kₙ is (n−1)!/2.
7. Colouring and matching: χ ≥ clique number; bipartite ⇒ χ ≤ 2; König only for **bipartite** graphs; α + β = n for any graph.
8. **Counting**: stars and bars needs the right form (≥ 0 vs ≥ 1); watch for "distinct vs identical" objects and "ordered vs unordered" groups; check overcounting by symmetry (divide by group size).
9. **Recurrences**: when the particular solution form clashes with a homogeneous root, multiply by n; check initial conditions last.
10. **Determinant**: det(kA) = kⁿdet(A) (not k·det); rank-nullity; singular ⇔ det = 0 ⇔ some eigenvalue 0 ⇔ rows dependent.
11. **Eigenvalues**: use trace and det first for 2×2 and 3×3 problems; for a triangular matrix read off the diagonal; Aᵏ, A⁻¹ eigenvalues transform simply.
12. **Linear systems**: compare ranks of A and \[A|b\]; with a parameter, find the values making det = 0 and test each separately.
13. **Limits**: always try L'Hôpital only for 0/0 or ∞/∞; for 1^∞ use e^(lim g(f−1)). Remember the Taylor expansions to evaluate limits quickly.
14. **Continuity vs differentiability**: |x| is continuous but not differentiable at 0; check both one-sided derivatives.
15. **Maxima/minima**: global extrema on a closed interval need endpoints; when f″ = 0 go to the next derivative.
16. **Integration**: use f(a+b−x) and even/odd tricks for definite integrals; Γ(1/2) = √π; check convergence conditions p > 1 or p \< 1.
17. **Probability**: mutually exclusive ≠ independent; Bayes problems need the total-probability denominator; "at least one" → 1 − none; E\[X + Y\] = E\[X\] + E\[Y\] always.
18. Distributions: memoryless property for exponential/geometric; Poisson mean = variance; Var(aX+b) = a²Var; for the normal, symmetrical probabilities use Φ(−z) = 1 − Φ(z).
19. Statistics: shifting data changes the mean but not SD; scaling affects both; the relation Mode = 3 Median − 2 Mean is the exam formula; variance of 1..n is (n²−1)/12.
20. Numerical answers: keep fractions exact until the end; NAT answers allow a range, but incorrect rounding early can push you out of it.

---

# PART F – Practice Plan

1. **Discrete Maths first** (about 6 to 8 marks): logic, sets/relations counting, lattices, groups, graph theorems, counting/recurrence/generating functions. Do topic-wise PYQs from 2010 to 2026.
2. **Linear Algebra and Probability** (about 4 to 5 marks): rank and eigenvalue shortcuts, Bayes, distributions. These repeat a lot; speed is the goal.
3. **Calculus** (about 2 to 3 marks): limits, MVT, maxima/minima, definite integrals; memorise the standard limits and series once.
4. Maintain an **error log** with the exact concept or trap behind each mistake; revise only that in the last two weeks.
5. Memorise Part E and the distribution table (D3) plus the standard counting numbers (Catalan, derangements, Bell). Practise NAT questions without a calculator.
6. Take full-length mocks; Engineering Maths gives some of the safest marks in the paper if you keep the formulas fresh.
