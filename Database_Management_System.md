# GATE 2027: Database Management Systems (Complete Revision Notes)

DBMS carries about 8-10 marks in GATE CS. The official syllabus is: **ER model, relational model (relational algebra, tuple calculus), SQL, integrity constraints, normal forms, file organization, indexing (B and B+ trees), transactions and concurrency control.** Cross-check once against the official GATE 2027 brochure.

**Marks-heavy topics:** Normalization, SQL, Transactions/Concurrency, B+ trees, Relational algebra. Master these first.

---

## 1. ER Model

**Basics**
- **Entity**: a thing. **Entity set**: a collection of similar entities. **Weak entity**: has no key of its own and depends on an owner (strong) entity through an identifying relationship. Its partial key is called the **discriminator**.
- **Attribute types**: simple/composite, single-valued/multivalued, stored/derived, key.
- **Cardinality ratios**: 1:1, 1:N, N:1, M:N.
- **Participation**: total (double line, every entity must participate) or partial.
- **Generalization** (bottom-up) and **specialization** (top-down). Constraints: disjoint/overlapping, total/partial.
- **Aggregation**: treats a relationship as a higher-level entity so it can participate in another relationship.

**ER to relational conversion (the source of many numericals)**

| Construct | Conversion |
|---|---|
| Strong entity | One table, the key becomes the primary key |
| Multivalued attribute | Separate table (entity key + attribute) |
| Composite attribute | Flatten into its components |
| 1:1 relationship | Merge into either side (prefer the total participation side), add FK |
| 1:N relationship | Put the FK on the **N side**, no new table |
| M:N relationship | **New table** with the keys of both sides (together form the primary key) |
| Weak entity | Table with (owner key + discriminator) as primary key |
| Ternary relationship | Separate table |

**Minimum tables trick:** M:N always needs its own table. 1:N and 1:1 can be merged. Total participation + 1:N on the N side lets you merge the relationship into the entity's table.

---

## 2. Relational Model and Keys

- **Relation** = a set of tuples. **Degree** = number of attributes. **Cardinality** = number of tuples.
- **Super key**: a set of attributes that uniquely identifies tuples. **Candidate key**: a minimal super key. **Primary key**: the chosen candidate key (no NULLs). **Alternate key**: the remaining candidates. **Foreign key**: references a primary/candidate key of another table.
- **Prime attribute**: part of some candidate key. **Non-prime**: part of none.

**Counting formulas (frequently asked)**
- Relation with n attributes and one candidate key of size k: number of super keys = **2^(n−k)**.
- Relation with n attributes, only attribute A as a candidate key: super keys = 2^(n−1).
- **Maximum number of candidate keys** with n attributes = **C(n, ⌊n/2⌋)**.
- If all attributes together form the only key, there is exactly 1 super key.
- Number of relations (subsets) with n attributes: 2ⁿ. Non-empty proper subsets: 2ⁿ − 2.

**Integrity constraints**
- **Domain**, **entity** (primary key not NULL), **referential** (an FK value must match an existing PK value, or be NULL).
- **Referential actions** on delete/update of the referenced row: `CASCADE`, `SET NULL`, `SET DEFAULT`, `NO ACTION/RESTRICT`.
- Inserting into the child with a nonexistent FK violates the constraint. Deleting from the parent with existing children violates it (unless cascaded).
- Violation cases to remember: **Insert** can violate domain, key, entity, and referential (on the child). **Delete** can violate referential (on the parent). **Update** can violate any of them.

---

## 3. Relational Algebra

**Operators**

| Operator | Symbol | Notes |
|---|---|---|
| Selection | σ | Filters rows, result size ≤ input |
| Projection | π | Picks columns, **removes duplicates** |
| Union, Intersection, Difference | ∪ ∩ − | Need **union compatible** relations |
| Cartesian product | × | \|R\| × \|S\| tuples |
| Rename | ρ | |
| Natural join | ⋈ | Equijoin on common attributes, common columns appear once |
| Theta join | ⋈θ | σθ(R × S) |
| Outer joins | ⟕ ⟖ ⟗ | Keep unmatched tuples, padded with NULL |
| Division | ÷ | "For all" queries |

**Basic (fundamental) operators:** σ, π, ∪, −, ×, ρ. Everything else (∩, ⋈, ÷) is derived.
- R ∩ S = R − (R − S).

**Size bounds**
- σ: 0 to |R|. π: 1 to |R|. R × S = |R|·|S|.
- Natural join of R, S with no common attributes = cross product. If the common attribute is a key of S and an FK in R: exactly **|R|** tuples (at most |R| tuples, and exactly |R| if the FK is non-null).
- Natural join with no matching values: 0 tuples.
- R ∪ S: max(|R|,|S|) to |R|+|S|. R ∩ S: 0 to min. R − S: 0 to |R|.

**Division**
- R(A,B) ÷ S(B) gives the A values that are related to **every** B in S.
- R ÷ S = π_A(R) − π_A((π_A(R) × S) − R).
- Keywords for division: "all", "every", "for each".

**Query equivalences**
- Selection can be pushed before a join (optimization). Cascade of σ can be reordered. Projection and selection commute only if the selection attributes are within the projected ones.

**Relational calculus**
- **Tuple relational calculus (TRC)**: `{t | P(t)}`, with ∃ and ∀. **Domain relational calculus (DRC)**: variables range over attribute values.
- Both are **declarative**. Safe TRC/DRC expressions are equivalent in power to relational algebra. Unsafe expressions (like `{t | ¬R(t)}`) produce infinite results, so they're disallowed.
- Relational algebra is **procedural**.

---

## 4. SQL

**Categories:** DDL (CREATE, ALTER, DROP, TRUNCATE), DML (SELECT, INSERT, UPDATE, DELETE), DCL (GRANT, REVOKE), TCL (COMMIT, ROLLBACK, SAVEPOINT).

**Logical order of execution (key to correct tracing)**
`FROM → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY`

**Essentials**
- `WHERE` filters rows before grouping and cannot use aggregates. `HAVING` filters groups after grouping.
- Every non-aggregate column in SELECT must appear in GROUP BY.
- `SELECT` does **not** remove duplicates by default, `DISTINCT` does.
- `UNION`, `INTERSECT`, `EXCEPT` remove duplicates. `UNION ALL` keeps them.
- `LIKE`: `%` = any string, `_` = exactly one character.
- `ORDER BY` default is ascending.

**NULL handling (classic trap source)**
- Any comparison with NULL (`=`, `<>`, `>`) yields **UNKNOWN**, and rows are kept only if the condition is TRUE.
- Use `IS NULL` / `IS NOT NULL`.
- Three-valued logic: TRUE AND UNKNOWN = UNKNOWN, FALSE AND UNKNOWN = FALSE, TRUE OR UNKNOWN = TRUE, NOT UNKNOWN = UNKNOWN.
- Aggregates: `COUNT(*)` counts all rows including NULLs. `COUNT(col)`, `SUM`, `AVG`, `MIN`, `MAX` **ignore NULLs**. On an empty set, `COUNT` = 0 and the others return NULL.
- `AVG(col)` ignores NULLs in both the sum and the count.
- **`NOT IN` with a NULL in the list returns no rows** (the NULL makes it UNKNOWN). `NOT EXISTS` doesn't have this issue.
- NULLs are treated as equal for `GROUP BY`, `DISTINCT`, and set operations.
- A `UNIQUE` column can contain multiple NULLs in most systems. A primary key cannot be NULL.

**Joins**
- `INNER JOIN`, `LEFT/RIGHT/FULL OUTER JOIN`, `CROSS JOIN`, `NATURAL JOIN`.
- Outer join conditions in `ON` vs `WHERE` behave differently: a `WHERE` condition on the right table of a left join can eliminate the NULL-padded rows.

**Subqueries**
- **Nested**: the inner query runs once. **Correlated**: the inner query runs per outer row and references the outer query's columns.
- `IN`, `EXISTS`, `ANY/SOME`, `ALL`. `> ALL` means greater than the maximum. `> ANY` means greater than the minimum.
- `ALL` on an empty subquery returns TRUE. `ANY` on an empty subquery returns FALSE.
- **Second highest salary:** `SELECT MAX(sal) FROM emp WHERE sal < (SELECT MAX(sal) FROM emp)`.
- **Employees who do X for all:** use `NOT EXISTS (... NOT EXISTS ...)` (the SQL form of division).

**Other features**
- **Views**: virtual tables. A view on a single table without aggregation is usually updatable. Views on joins or aggregates generally are not.
- **Constraints**: `PRIMARY KEY`, `UNIQUE`, `NOT NULL`, `CHECK`, `FOREIGN KEY`, `DEFAULT`.
- **Triggers**: event-condition-action (before/after, row/statement level).
- **DELETE** removes rows (can be rolled back, with WHERE). **TRUNCATE** removes all rows (DDL, faster, resets). **DROP** removes the table structure.
- **Embedded SQL / cursors**: used to process result sets row by row.
- `HAVING` without `GROUP BY` treats the whole table as one group.

---

## 5. Functional Dependencies and Normalization (Highest-Weightage Area)

### 5.1 Functional dependencies

**Armstrong's axioms (sound and complete):**
1. **Reflexivity**: if Y ⊆ X, then X → Y.
2. **Augmentation**: if X → Y, then XZ → YZ.
3. **Transitivity**: if X → Y and Y → Z, then X → Z.

**Derived rules:** Union (X→Y, X→Z ⇒ X→YZ), Decomposition (X→YZ ⇒ X→Y, X→Z), Pseudotransitivity (X→Y, WY→Z ⇒ WX→Z).

**Attribute closure X⁺** is the set of attributes determined by X. It is how you do almost everything:
- **Checking X → Y:** compute X⁺, and check if Y ⊆ X⁺.
- **Super key test:** X⁺ = all attributes. **Candidate key:** a super key where no proper subset is a super key.
- **Equivalence of two FD sets F and G:** F covers G if every FD in G can be derived from F (and vice versa).

**Finding candidate keys (fast method)**
1. Attributes that **never appear on the RHS** of any FD must be in every candidate key.
2. Attributes that appear **only on the RHS** are in no candidate key.
3. Take the must-have attributes and compute the closure. If it covers everything, that is the key. Otherwise add other attributes one at a time.
4. Check for other candidate keys by looking for attributes that are determined by other parts (e.g. if A → B and B is in a key, swapping B with A may give another key).

**Canonical (minimal) cover**
1. Split RHS so each FD has a single attribute on the right.
2. Remove **extraneous attributes** from the LHS.
3. Remove **redundant FDs** (those derivable from the rest).

The minimal cover is not always unique.

### 5.2 Normal forms

| NF | Condition |
|---|---|
| **1NF** | All attributes atomic, no repeating groups or multivalued attributes |
| **2NF** | 1NF + no **partial dependency** (no non-prime attribute depends on a proper subset of a candidate key) |
| **3NF** | For every non-trivial X → A: **X is a super key, or A is a prime attribute** |
| **BCNF** | For every non-trivial X → A: **X is a super key** |

Hierarchy: BCNF ⊂ 3NF ⊂ 2NF ⊂ 1NF.

**Important facts (frequent true/false)**
- If every attribute is prime, the relation is in **3NF** automatically (but not necessarily BCNF).
- If all candidate keys are **single attributes**, there can be no partial dependency, so the relation is at least **2NF**.
- A relation with only **two attributes** is always in BCNF.
- A relation with no non-trivial FDs is in BCNF.
- Checking highest normal form: test BCNF first. If it fails, test 3NF (prime check). If that fails, test 2NF (partial dependency). Otherwise 1NF.
- For BCNF, only check FDs whose LHS is **not** a super key. For 3NF, the exemption is that the RHS is prime.

### 5.3 Decomposition

**Lossless-join decomposition**
- Decomposition of R into R1, R2 is lossless iff **(R1 ∩ R2) → R1 or (R1 ∩ R2) → R2**, i.e. the common attributes form a key of at least one side.
- For more than two tables, use the **chase / tableau method**.
- A lossy decomposition generates **spurious tuples** (the natural join gives extra tuples), so the join has ≥ the original tuples.

**Dependency preservation**
- Every FD in F must be derivable from the union of the FDs projected onto the decomposed tables.

**Guarantees**

| Decomposition into | Lossless | Dependency preserving |
|---|---|---|
| **BCNF** | Always possible | **Not always** |
| **3NF** | Always possible | Always possible |

- The **3NF synthesis algorithm** (from a canonical cover) gives a lossless, dependency-preserving 3NF decomposition.
- Standard example: R(A,B,C) with AB → C and C → B. It is in 3NF, but not in BCNF, and the BCNF decomposition loses the dependency AB → C.

### 5.4 Higher normal forms (conceptual)
- **Multivalued dependency (MVD)** X →→ Y. **4NF**: no non-trivial MVD unless X is a super key.
- **5NF** deals with join dependencies.
- Every BCNF relation with only FDs is not necessarily in 4NF, and 4NF ⊂ BCNF.

### 5.5 Denormalization
- Deliberately introducing redundancy to speed up reads, at the cost of update anomalies. Anomalies in un-normalized tables: insertion, deletion, update.

---

## 6. File Organization and Indexing

### 6.1 Disk and record basics
- **Blocking factor** bf = ⌊Block size / Record size⌋ (records do not span blocks in unspanned organization).
- **Number of blocks** for a file = ⌈N / bf⌉.
- With spanned records, no space is wasted in blocks and bf = Block size / Record size (fractional).
- **Search cost**: heap file (unsorted) ≈ b/2 on average, b worst case. Sorted file with binary search ≈ ⌈log₂ b⌉ block accesses.

### 6.2 Types of indexes

| Type | Description |
|---|---|
| **Primary index** | On the **ordering key** field, **sparse** (one entry per block) |
| **Clustering index** | On an ordering **non-key** field, sparse (one entry per distinct value) |
| **Secondary index** | On a non-ordering field, **dense** (an entry for every record or value) |

- **Dense index**: an entry for every search-key value. **Sparse index**: an entry per block only.
- A file can have **only one** primary/clustering index, but **many** secondary indexes.
- Index size: entries = number of blocks (sparse) or number of records (dense). Index blocks = ⌈entries / index blocking factor⌉, where index bf = ⌊B / (key + pointer size)⌋.
- **Multilevel index**: an index on the index, until one block remains. Number of levels = ⌈log_{fanout}(entries)⌉ roughly. Search cost = number of levels + 1 (for the data block).
- **Single-level index search**: ⌈log₂(index blocks)⌉ + 1.

### 6.3 B-Tree and B+ Tree (very high weightage)

**B+ tree properties**
- All data pointers are in the **leaf nodes**. Leaves are **linked** (good for range queries). Internal nodes only hold keys for routing.
- All leaves are at the **same level** (balanced).
- Since the tree is balanced, search cost = **height + 1** disk accesses (traverse the height, then fetch the data block).

**Order of a B+ tree (p = maximum number of tree pointers per internal node)**
- Internal node: **p·P + (p − 1)·K ≤ B** (P = block pointer size, K = key size, B = block size).
- Leaf node: **p_leaf·(K + Pr) + P ≤ B** (Pr = record pointer size, P = next-leaf pointer), where p_leaf is the maximum number of keys per leaf.

**Occupancy rules (order p = max children)**

| Node | Min children / keys | Max |
|---|---|---|
| Internal | ⌈p/2⌉ children | p children, p − 1 keys |
| Leaf | ⌈(p − 1)/2⌉ keys | p − 1 keys |
| Root (internal) | 2 children | p children |
| Root (leaf) | 1 key | p − 1 keys |

Always follow the **definition given in the question** (some define order by max keys). Do not memorize one convention blindly.

**B-tree vs B+ tree**

| B-Tree | B+ Tree |
|---|---|
| Keys and data pointers in all nodes | Data pointers only in leaves |
| Fewer keys per node (fan-out is lower) | Higher fan-out, shallower tree |
| No duplicate keys across levels | Keys in internal nodes are **repeated** in leaves |
| Range queries harder | Leaves are linked, so range queries are easy |
| Search can end at an internal node | Search always goes to a leaf |

**Height and size**
- Maximum records ≈ (max pointers per node)^height × (keys per leaf). Minimum height is reached with maximum occupancy.
- Insertion: insert into a leaf. On overflow, **split**. For a leaf split, the middle key is **copied up** (B+ tree). For an internal node split, the middle key is **moved up**. For a B-tree, the middle key always moves up.
- Deletion: remove the key, and on underflow **borrow** from a sibling or **merge**.
- Root splitting is the only way a tree grows taller.
- Tracing split and merge steps carefully is a common numerical.

### 6.4 Hashing
- **Static hashing**: fixed number of buckets, uses overflow chaining. Performance degrades as the file grows.
- **Extendible hashing**: a directory of size 2^(global depth) that doubles when a bucket with local depth = global depth splits. No overflow chains, one extra directory lookup.
- **Linear hashing**: grows gradually, one bucket at a time, without a directory.
- Collision handling: chaining or open addressing (linear probing, quadratic probing, double hashing).
- Hash is good for **equality** searches, bad for **range** queries.

### 6.5 Query processing and cost (occasionally asked)
- **External merge sort** with M buffer pages on b blocks: pass 0 creates ⌈b/M⌉ runs. Each later pass merges M − 1 runs. Total passes = 1 + ⌈log_{M−1}⌈b/M⌉⌉. Each pass costs 2b I/Os (read + write).
- **Block nested-loop join**: b_r + ⌈b_r/(M − 2)⌉ · b_s. **Simple nested-loop join** (tuple at a time): b_r + n_r · b_s.
- **Sort-merge join**: sort cost + b_r + b_s. **Hash join**: about 3(b_r + b_s). The smaller relation should be the outer relation in a nested-loop join.
- **Query optimization** heuristics: push selections and projections down, do the most restrictive operations first, avoid cross products.

---

## 7. Transactions

**ACID**
- **Atomicity** (all or nothing, ensured by the recovery manager), **Consistency** (moves the DB from one consistent state to another, the programmer's responsibility), **Isolation** (concurrent execution looks serial, the concurrency control manager), **Durability** (committed changes survive failure, recovery manager).

**Transaction states:** Active → Partially committed → Committed. Active/Partially committed → Failed → Aborted.

**Read-write anomalies (from concurrent schedules)**
- **Dirty read** (WR): read uncommitted data.
- **Unrepeatable read** (RW): the same item read twice gives different values.
- **Lost update** (WW): one write overwrites another's update.
- **Phantom**: a repeated range query returns new rows.

**Isolation levels (SQL)**

| Level | Dirty read | Non-repeatable read | Phantom |
|---|---|---|---|
| Read uncommitted | Possible | Possible | Possible |
| Read committed | No | Possible | Possible |
| Repeatable read | No | No | Possible |
| Serializable | No | No | No |

### 7.1 Schedules and serializability

- **Serial schedule**: transactions run one after another. n transactions have **n!** serial schedules.
- Number of possible schedules (interleavings) for transactions with n₁, n₂, …, nₖ operations = **(n₁ + n₂ + … + nₖ)! / (n₁! · n₂! · … · nₖ!)**.
- **Conflicting operations**: same data item, different transactions, at least one is a write (RW, WR, WW).

**Conflict serializability**
- Build the **precedence graph** (edge Ti → Tj if an operation of Ti conflicts with and precedes one of Tj). The schedule is conflict serializable **iff the graph has no cycle**.
- A topological order of the graph gives an equivalent serial schedule.
- **Number of equivalent serial schedules** = number of valid topological orderings.

**View serializability**
- Conditions: the same initial reads, the same reads-from relationships, the same final writes.
- **Conflict serializable ⊂ view serializable**. A schedule that is view but not conflict serializable must contain **blind writes** (a write without a prior read).
- Testing view serializability is **NP-complete**.
- If a schedule has no blind writes, view serializable = conflict serializable.

**Hierarchy:** Serial ⊂ Conflict serializable ⊂ View serializable ⊂ All schedules.

### 7.2 Recoverability

| Class | Condition |
|---|---|
| **Recoverable** | If Tj reads from Ti, then Ti commits **before** Tj commits |
| **Cascadeless (ACA)** | Tj reads a value only after Ti that wrote it has **committed** |
| **Strict** | No transaction reads **or overwrites** a value written by an uncommitted transaction |

- **Strict ⊂ Cascadeless ⊂ Recoverable.**
- Serializability and recoverability are **independent** properties. A schedule can be serializable but not recoverable, and vice versa.
- **Irrecoverable schedule**: a transaction commits after reading dirty data, and the writer later aborts.
- Cascading rollback happens when a schedule is recoverable but not cascadeless.

### 7.3 Concurrency control

**Lock modes:** Shared (S), Exclusive (X). Compatibility: only S-S is compatible.
- **Multiple granularity locking**: intention locks IS, IX, SIX.
  - Compatibility: IS is compatible with IS, IX, S, SIX. IX is compatible with IS, IX. S is compatible with IS, S. SIX is compatible with IS only. X is compatible with nothing.

**Two-Phase Locking (2PL)**
- **Growing phase** (acquire locks only), then **shrinking phase** (release locks only). Once a lock is released, no new lock can be acquired.
- The **lock point** is the point where the last lock is obtained. Serial order follows lock point order.
- 2PL **guarantees conflict serializability**, but it **does not guarantee freedom from deadlock**, and does **not guarantee** recoverability or cascadelessness.
- There are schedules that are conflict serializable but cannot be produced by 2PL.

| Variant | Behavior | Extra guarantee |
|---|---|---|
| **Strict 2PL** | Holds all **exclusive** locks until commit/abort | Strict schedules (hence cascadeless), still may deadlock |
| **Rigorous 2PL** | Holds **all** locks (S and X) until commit/abort | Serial order = commit order, cascadeless |
| **Conservative 2PL** | Acquires all locks **before** starting | **Deadlock-free**, but may not be practical |

**Deadlock in locking**
- Detect using the **wait-for graph** (a cycle means deadlock).
- **Prevention schemes (using timestamps, older = smaller TS):**
  - **Wait-Die** (non-preemptive): if the requester is **older**, it **waits**. If younger, it **dies** (aborts).
  - **Wound-Wait** (preemptive): if the requester is **older**, it **wounds** (aborts) the holder. If younger, it **waits**.
  - Both prevent deadlock and starvation (restarted transactions keep their original timestamp).
- Timeout-based detection is another approach.

**Timestamp ordering protocol**
- Each transaction gets a timestamp TS(T). Each item Q has **R-TS(Q)** (largest TS that read it) and **W-TS(Q)** (largest TS that wrote it).
- **Read(Q) by T:** if TS(T) < W-TS(Q), the read is too late, so **abort/rollback T**. Otherwise allow it and set R-TS(Q) = max(R-TS(Q), TS(T)).
- **Write(Q) by T:** if TS(T) < R-TS(Q), **rollback T**. If TS(T) < W-TS(Q), **rollback T** (basic protocol). Otherwise allow it and set W-TS(Q) = TS(T).
- **Thomas write rule**: if TS(T) < W-TS(Q) (but ≥ R-TS(Q)), **ignore the write** instead of aborting. This allows some schedules that are view serializable but not conflict serializable.
- Basic timestamp ordering guarantees **conflict serializability and deadlock freedom**, but **starvation is possible**, and it is **not guaranteed recoverable/cascadeless**.

**Other schemes (conceptual):** optimistic (validation-based) concurrency control has read, validation, and write phases. **Multiversion concurrency control (MVCC)** lets readers see older versions without blocking.

### 7.4 Recovery (low priority for GATE, but conceptually useful)
- **Write-ahead logging (WAL)**: log records must be written to stable storage **before** the corresponding data page is written.
- **Log record types:** `<T, start>`, `<T, X, old, new>`, `<T, commit>`, `<T, abort>`.
- **Deferred update**: writes to the DB happen only at commit (needs **redo**, no undo). **Immediate update**: writes can happen before commit (needs both **undo and redo**).
- **Checkpoint**: limits how far back recovery must scan. Transactions committed before the checkpoint need no redo.
- Recovery procedure: **redo** transactions with both start and commit in the log, **undo** those with start but no commit.
- **ARIES:** three phases, **Analysis → Redo → Undo**. It follows the steal/no-force policy and uses compensation log records (CLRs).
- **Steal**: dirty pages of uncommitted transactions can be written to disk (needs undo). **No-force**: pages need not be flushed at commit (needs redo).

---

## 8. Quick Formula Sheet

| Topic | Formula |
|---|---|
| Super keys (n attrs, one key of size k) | 2^(n−k) |
| Max candidate keys (n attrs) | C(n, ⌊n/2⌋) |
| Blocking factor | ⌊B / R⌋ |
| Blocks for N records | ⌈N / bf⌉ |
| Binary search on sorted file | ⌈log₂ b⌉ |
| Dense index entries | Number of records |
| Sparse index entries | Number of data blocks |
| B+ tree search cost | Height + 1 |
| B+ internal node order | p·P + (p − 1)·K ≤ B |
| Number of serial schedules | n! |
| Number of interleavings | (Σnᵢ)! / Π(nᵢ!) |
| Lossless binary decomposition | (R1 ∩ R2) → R1 or R2 |
| External sort passes | 1 + ⌈log_{M−1}⌈b/M⌉⌉ |
| Block nested-loop join cost | b_r + ⌈b_r/(M−2)⌉·b_s |

---

## 9. Recurring GATE Question Types

1. **Normalization**: find candidate keys, highest normal form, check lossless/dependency-preserving decomposition, minimal cover, count super keys.
2. **SQL output tracing**: GROUP BY/HAVING, NULL behavior, NOT IN with NULL, correlated subqueries, joins and outer joins, query meaning (what does this query compute).
3. **Relational algebra**: result size bounds, equivalent expressions, division, relational algebra vs SQL vs TRC.
4. **Serializability**: draw the precedence graph, conflict vs view serializable, count equivalent serial orders, count the number of schedules.
5. **Recoverability**: classify a schedule as recoverable, cascadeless, or strict.
6. **2PL and timestamp**: which schedule is allowed under 2PL/strict 2PL/rigorous 2PL, and timestamp abort checks, Thomas write rule.
7. **B+ tree**: order calculation, maximum keys, minimum/maximum height, number of nodes, tracing insertion/deletion, disk access count.
8. **Indexing and file organization**: number of blocks, index sizes, levels of a multilevel index, access cost comparisons.
9. **ER to tables**: minimum number of tables, which side holds the FK.
10. **Conceptual true/false**: ACID, deadlock handling, wait-die vs wound-wait, isolation levels, 2PL properties, dense vs sparse index.

---

## 10. Common Traps (Read Before Every Mock)

- **Candidate key search**: don't forget attributes that never appear on any RHS. They belong to every key.
- **BCNF vs 3NF**: 3NF allows a prime RHS. Always test BCNF first, then 3NF.
- **Projection removes duplicates** in relational algebra but not in plain SQL `SELECT`.
- **`NOT IN` and NULL** returns empty. `COUNT(*)` vs `COUNT(col)` differs with NULLs.
- **A cycle in the precedence graph** means not conflict serializable, but the schedule might still be view serializable (check for blind writes).
- **2PL ≠ deadlock-free** and ≠ recoverable. Only strict/rigorous variants add cascadelessness. Only conservative is deadlock-free.
- **Timestamp ordering** is deadlock-free but not automatically recoverable.
- **Wait-die**: older waits, younger dies. **Wound-wait**: older wounds, younger waits. Mixing these up is very common.
- **B+ tree leaf split** copies the key up, and the **internal split** moves it up.
- **Dense vs sparse**: primary index is sparse, secondary index is dense.
- **Lossless ≠ dependency-preserving.** They are independent properties.
- **Read the order definition** in B+ tree questions before computing node capacity.

---

## 11. How to Use These Notes

1. Do **15-20 previous-year questions per section**. Prioritize normalization, transactions, SQL, and B+ trees.
2. Numerical topics (candidate keys, precedence graphs, B+ tree order/height, index sizing) should become mechanical.
3. Whenever a PYQ exposes a gap, return to that section and re-derive the concept rather than memorizing the answer.
4. Revise the **Traps** section (Section 10) the day before each mock test and again a week before the exam.
