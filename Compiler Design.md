# GATE 2027 – Compiler Design: Complete Revision Notes

Syllabus (GATE 2027 CS): *Lexical analysis, parsing, syntax-directed translation. Runtime environments. Intermediate code generation. Local optimisation, data flow analyses: constant propagation, liveness analysis, common sub-expression elimination.*

Compiler Design gives about 5 to 8 marks and is very **formulaic**: FIRST/FOLLOW, parsing tables, LR item sets, SDT outputs, three-address code, data-flow equations. Practise these by hand.

---

## 1. Phases of a Compiler

| Phase | Input → Output | Typical errors found |
| --- | --- | --- |
| **Lexical analysis** (scanner) | characters → tokens | illegal characters, malformed numbers, unterminated strings |
| **Syntax analysis** (parser) | tokens → parse/syntax tree | missing `;`, unbalanced parentheses |
| **Semantic analysis** | tree → annotated tree | type mismatch, undeclared variable, wrong number of arguments |
| **Intermediate code generation** | tree → three-address code (TAC) | – |
| **Code optimisation** | TAC → better TAC | – |
| **Code generation** | TAC → target code | – |

- Symbol table (names, types, scope, offsets) and error handler are used by all phases.
- **Front end** (analysis: lexical → intermediate code, machine independent) and **back end** (synthesis: optimisation + code generation, machine dependent). Front end for m languages + back end for n machines → m + n components instead of m × n.
- **Pass** = one read over the program; a single pass can include several phases. **Cross compiler** runs on one machine and generates code for another. **Bootstrapping** uses a compiler written in its own language. Interpreter executes directly (slower, no object code). Preprocessor → compiler → assembler → linker → loader.
- Logical errors (wrong algorithm) are never detected by a compiler. Undeclared variable is a **semantic** error; missing semicolon is **syntactic**.

---

## 2. Lexical Analysis

- **Token** = (token name, attribute); **pattern** = rule (regex) describing lexemes; **lexeme** = the actual matched string.
- Specified by **regular expressions** → **NFA** (Thompson's construction; ≤ 2·|r| states) → **DFA** (subset construction) → **minimised DFA**. A lexer is a DFA simulation.
- Rules: **longest match** (`==` before `=`; `ifx` is an identifier, not `if` + `x`), and when two patterns match the same length, the **earlier rule** wins (keywords listed before identifiers).
- Comments and whitespace are discarded (not tokens). A string literal is **one** token. Count tokens in `printf("i = %d, &i = %x", i, &i);` → `printf ( "..." , i , & i ) ;` = **10 tokens**.
- Tools: Lex/Flex (generates a scanner), Yacc/Bison (generates an LALR(1) parser).
- Input buffering: two-buffer scheme with **sentinels** (end-of-buffer marker) to reduce end checks.
- A lexer recognises only **regular** languages: it cannot check balanced parentheses or nesting. It also doesn't detect undeclared variables or type errors. It may report invalid tokens, but typically cannot catch a missing semicolon.
- Error recovery in the lexer: delete a character, insert, replace, transpose.

---

## 3. Syntax Analysis (Parsing)

### 3.1 Grammars

- CFG `G = (V, T, P, S)`. **Leftmost derivation** (expand leftmost non-terminal; matches top-down), **rightmost derivation** (bottom-up parsers produce it in **reverse**).
- **Ambiguous** grammar: a string with two parse trees (or two leftmost derivations). Classic: `E → E+E | E*E | id`, and dangling else. Ambiguity is undecidable in general. Remove by encoding precedence/associativity: `E → E+T | T; T → T*F | F; F → (E) | id`. Inherently ambiguous languages exist. An ambiguous grammar cannot be LL(1) or LR(k).
- **Left recursion elimination**: `A → Aα | β` ⇒ `A → βA'`, `A' → αA' | ε`. (Needed for top-down parsing.) Indirect left recursion needs ordering of non-terminals.
- **Left factoring**: `A → αβ₁ | αβ₂` ⇒ `A → αA'`, `A' → β₁ | β₂`.
- Left recursion gives **left associativity**; right recursion gives right associativity (e.g. exponent, assignment). Lower in the grammar (deeper) = higher precedence.

### 3.2 FIRST and FOLLOW

- **FIRST(X)**: terminals that begin strings derived from X (ε included if X ⇒\* ε). For a terminal a: {a}. For `X → Y₁Y₂…`: add FIRST(Y₁) − ε; if Y₁ ⇒\* ε add FIRST(Y₂) − ε; … ; add ε if all derive ε.
- **FOLLOW(A)**: terminals that can appear immediately to the right of A. FOLLOW(S) contains `$`. For `B → αAβ`: FOLLOW(A) ⊇ FIRST(β) − ε; if β ⇒\* ε (or β empty): FOLLOW(A) ⊇ FOLLOW(B). FOLLOW never contains ε.
- Example for `E → TE'; E' → +TE' | ε; T → FT'; T' → *FT' | ε; F → (E) | id`: FIRST(E) = FIRST(T) = FIRST(F) = { (, id }; FIRST(E') = { +, ε }; FIRST(T') = { \*, ε }; FOLLOW(E) = FOLLOW(E') = { ), $ }; FOLLOW(T) = FOLLOW(T') = { +, ), $ }; FOLLOW(F) = { \*, +, ), $ }.

### 3.3 Top-down parsing

- **Recursive descent** (may backtrack), **predictive parsing** = non-recursive LL(1) with a stack and a table M\[A, a\].
- **Table construction**: for each `A → α`: for every a in FIRST(α) put the production in M\[A, a\]; if ε ∈ FIRST(α) put it in M\[A, b\] for every b in FOLLOW(A) (including `$`). A multiply-defined entry ⇒ not LL(1).
- **LL(1) condition** for `A → α | β`: (1) FIRST(α) ∩ FIRST(β) = ∅; (2) at most one of α, β derives ε; (3) if β ⇒\* ε then FIRST(α) ∩ FOLLOW(A) = ∅.
- LL(1) grammars are **unambiguous**, **not left-recursive**, and **left factored**. LL(1) ⊂ LR(1). LL parsers build the tree top-down, left to right, leftmost derivation, with 1 token of lookahead.
- Predictive parser stack contents/moves are asked as "number of steps", "contents after reading k tokens". Practise tracing.

### 3.4 Bottom-up (shift-reduce) parsing

- Actions: **shift**, **reduce** (by a production), **accept**, **error**. A **handle** is the right-sided substring matching a production whose reduction is a step of a reverse rightmost derivation. **Viable prefix**: a prefix of a right-sentential form that can appear on the stack.
- **Conflicts**: shift/reduce, reduce/reduce. Ambiguous grammars cause conflicts. Yacc resolves shift/reduce by **shifting** (unless precedence declared) and reduce/reduce by choosing the **earlier** production.
- Counting facts for a successful parse of a string with t tokens: **shifts = t**; **reductions = number of internal nodes (production applications)** in the parse tree.

### 3.5 LR parsers

- **LR(0) item**: production with a dot, `A → α·β`. An ε-production gives one item `A → ·`; a production with RHS length k gives **k + 1** items. **Closure** adds `B → ·γ` for each `A → α·Bβ`; **goto(I, X)** moves the dot over X. Augment the grammar with `S' → S`.
- Table construction:
  - **LR(0)**: reduce `A → α·` in **every** terminal column (conflicts easily).
  - **SLR(1)**: reduce `A → α·` only on **FOLLOW(A)**.
  - **CLR / LR(1)**: items `[A → α·β, a]` with lookahead; reduce only on the item's lookahead; many more states.
  - **LALR(1)**: merge LR(1) states with the **same core** (same LR(0) items), union of lookaheads. Same number of states as LR(0)/SLR. Merging can create **reduce/reduce conflicts**, but never new shift/reduce conflicts.
- **Power**: LR(0) ⊂ SLR(1) ⊂ LALR(1) ⊂ CLR(1) = LR(1). In terms of languages, LR(1) = deterministic CFLs. **Number of states**: `LR(0) = SLR = LALR ≤ CLR`.
- Classic grammar `S → L = R | R; L → *R | id; R → L` is **not SLR(1)** (shift/reduce on `=`) but is LR(1)/LALR(1).
- Standard expression grammar (`E → E+T|T; T → T*F|F; F → (E)|id`): the LR(0) automaton has **12 states**; it is SLR(1).
- **Operator precedence parsing**: for operator grammars (no ε, no two adjacent non-terminals); table built from precedence relations ⋖, ≐, ⋗.
- **Error recovery**: panic mode (skip to synchronising tokens, e.g. `;`), phrase-level (local correction), error productions, global correction (minimum edits).
- **Parser comparison**: top-down is easy to write by hand but cannot handle left recursion; bottom-up handles a larger class (left recursion fine) but tables are large and generated by tools.

---

## 4. Syntax-Directed Translation (SDT)

- **Syntax-directed definition (SDD)**: grammar + semantic rules attached to productions. **Synthesized attribute**: computed from children (and own attributes). **Inherited attribute**: computed from parent and/or siblings. **Annotated parse tree** shows attribute values; **dependency graph** must be acyclic to evaluate.
- **S-attributed SDD**: synthesized attributes only; evaluated **bottom-up** in one pass, naturally with an LR parser (actions at reductions). **L-attributed SDD**: each inherited attribute of a symbol depends only on the parent and the symbols to its **left**; can be evaluated in one left-to-right depth-first pass (works with LL parsing). **Every S-attributed definition is L-attributed.**
- **SDT scheme**: semantic actions in braces placed in the RHS. **Postfix SDT**: all actions at the right end (S-attributed, executed when reducing). Inherited attributes in bottom-up parsing need marker non-terminals (ε-productions).
- Output order: for `E → E1 + T {print('+')}` style schemes, the sequence of prints gives the **postfix** translation (for infix to postfix). Actions placed in the middle execute when the parser reaches that point in a depth-first traversal (top-down) or after the preceding symbols have been reduced (bottom-up).
- **Typical GATE question**: given a grammar with `{ print ... }` actions and an input string, give the printed output. Method: build the parse tree, then traverse in the order dictated by action positions (do a left-to-right DFS, executing actions when reached).
- **Applications**: expression evaluation, building syntax trees/DAGs, type checking, TAC generation, counting (e.g. number of 1s in a binary string), converting numbers.
- **Semantic analysis**: type checking (static vs dynamic), type coercion/conversion, overloading, scope resolution using the symbol table (static/lexical scope). Syntax tree vs **DAG** (DAG shares common subexpressions).

---

## 5. Runtime Environments

### 5.1 Storage organisation

- Typical layout: **Code** (static), **Static data** (globals, constants), **Heap** (dynamic, `malloc`, grows up), **Stack** (activation records, grows down). Free memory sits between heap and stack.
- **Activation record (frame)** fields: actual parameters, return value, **control link** (dynamic link: caller's frame), **access link** (static link: lexical parent's frame), saved machine status (return address, registers), local data, temporaries. Allocated on a stack at call, popped at return → supports **recursion**.
- **Activation tree** shows calls; control stack holds the path from root to the current node. Procedure lifetime = activation lifetime.
- **Calling sequence**: caller evaluates arguments, pushes return address and links; callee saves registers and allocates locals; return reverses the steps.

### 5.2 Scope

- **Static (lexical) scope**: a name refers to the nearest enclosing *textual* declaration (C, Java, Pascal). **Dynamic scope**: a name refers to the most recent binding on the call stack at runtime.
- Example: `int x = 10; void f(){ print(x); } void g(){ int x = 20; f(); }` → static scope prints 10, dynamic scope prints 20.
- **Access to non-local names**: access links (follow `n_current − n_declared` links), or a **display** (array of pointers per nesting level: O(1) access).
- Block structure, nested procedures, shadowing; **dangling references** arise when a stack/heap object is freed but references remain.

### 5.3 Parameter passing

| Mechanism | Behaviour |
| --- | --- |
| **Call by value** | Callee gets a copy of the value; caller's variable unchanged |
| **Call by reference** | Callee gets the address; changes affect the caller immediately; aliasing possible |
| **Copy-restore (value-result)** | Copy in at call, copy out at return; differs from reference when the same variable is reachable twice (aliasing) or changed through a global during the call |
| **Call by name** | The argument expression is **re-evaluated** every time the parameter is used (textual substitution, thunks); `swap(i, a[i])` breaks because `i` changes between uses |

- GATE tracing technique: write the values of all variables at call and at return for each mechanism; for copy-restore remember to **assign back at the end**.

### 5.4 Heap management and garbage collection

- Allocation strategies: first fit/best fit, free lists; fragmentation (internal/external); **compaction**.
- **Garbage collection**: **reference counting** (cannot reclaim cyclic structures; overhead on each pointer update), **mark-and-sweep** (mark reachable, sweep unreachable; pauses, fragmentation), **copying/compacting** collectors, generational GC. Memory leaks and dangling pointers are programmer errors in C.

---

## 6. Intermediate Code Generation

### 6.1 Representations

- **Three-address code (TAC)**: at most one operator on the right: `x = y op z`, `x = op y`, `x = y`, `goto L`, `if x relop y goto L`, `param x`, `call p, n`, `return`, `x = a[i]`, `a[i] = x`, `x = *p`, `*p = x`. Names of temporaries are created for subexpressions.
- Example: `a = b * -c + b * -c` → `t1 = minus c; t2 = b * t1; t3 = minus c; t4 = b * t3; t5 = t2 + t4; a = t5` (5 temporaries, 6 instructions, without CSE). With a DAG, the repeated `b * -c` is shared: `t1 = minus c; t2 = b * t1; t3 = t2 + t2; a = t3` (3 temporaries, 4 instructions).
- **Quadruples**: (op, arg1, arg2, result) – four fields, results are explicit names, easy to reorder. **Triples**: (op, arg1, arg2) – results referred to by statement number (three fields); moving code requires renumbering. **Indirect triples**: a list of pointers to triples; allows reordering without changing the triples. Storage: quadruples > triples; number of fields: 4 / 3 / 3 (+ pointer list).
- **Syntax tree vs DAG**: the DAG for an expression has one node for each distinct subexpression (value-numbering method). Number of nodes in the DAG = number of distinct operators/operands after sharing.
- **Static single assignment (SSA)**: every variable assigned exactly once; φ-functions at join points.
- Postfix notation, stack machine code are other IR forms.

### 6.2 Translation of constructs

- **Arrays**: for `A[i]` 1-D with lower bound `low`, width `w`: address = base + (i − low) × w. 2-D row-major `A[i][j]` (n₂ columns): base + ((i − low₁) × n₂ + (j − low₂)) × w. Column-major swaps roles.
- **Boolean expressions**: either numeric (0/1) or flow of control with **short-circuit** evaluation; jumping code with true/false labels. **Backpatching**: generate jumps with unfilled targets, keep lists (`truelist`, `falselist`, `nextlist`), fill in when the target is known; makes one-pass generation possible.
- Control flow: `if`, `while`, `for`, `switch` become conditional/unconditional jumps; typically generates labels L1, L2 in order.
- Procedure calls: `param` instructions followed by `call`.
- Type conversion, declarations (offsets in symbol table).

### 6.3 Basic blocks and flow graph

- **Basic block**: maximal straight-line code with one entry (first instruction) and one exit (last).
- **Leaders**: (1) the first instruction; (2) any target of a conditional or unconditional jump; (3) any instruction immediately **following** a jump. **Number of basic blocks = number of leaders.** Each block extends from a leader up to (but not including) the next leader.
- **Flow graph**: nodes = blocks; edge B → C if C can follow B (fall-through or jump).
- **Dominators**: d dominates n if every path from the entry to n goes through d. **Back edge**: edge n → d where d dominates n. **Natural loop** of a back edge: d plus all nodes that can reach n without passing through d; **header** d has the single entry. **Reducible** flow graphs have all loops natural; the number of iterations of the iterative data-flow algorithm is bounded by loop depth + a constant.

---

## 7. Code Optimisation

Optimisation must preserve program semantics, be worth the effort, and improve speed/space.

### 7.1 Local (within a basic block) – DAG based

- **Common subexpression elimination (CSE)**: reuse a value already computed (valid if operands unchanged in between). **Copy propagation**: replace `x` by `y` after `x = y`. **Constant folding** (`2 * 3` → 6) and **constant propagation** (`x = 5; y = x + 1` → `y = 6`). **Dead code elimination**: remove assignments to variables never used afterwards (needs liveness). **Algebraic simplification** (`x + 0`, `x * 1`, `x * 2 → x << 1`), **strength reduction** (replace expensive ops by cheaper: `x * 2` → `x + x`; in loops, `i * 4` → incremental addition), **unreachable code removal**.
- **DAG construction for a block**: leaf per variable's initial value, interior node per operator; nodes with the same operator and children are shared (value numbering). Variables attached to a node are its aliases; node with no attached live variable is dead. Typical question: "number of nodes in the DAG" or "minimum number of TAC statements after optimisation".

### 7.2 Peephole optimisation

- Examine a small sliding window of target instructions: eliminate redundant loads/stores (`ST a, R; LD R, a`), unreachable code, **flow-of-control** optimisations (jump to jump), algebraic simplification, strength reduction, machine idioms (`INC`).

### 7.3 Loop optimisations

- **Code motion**: move **loop-invariant** computations to the pre-header. **Induction variable elimination** and **strength reduction** (`j = 4*i` → `j = j + 4`). **Loop unrolling**, loop fusion, loop jamming.
- Loops are important because programs spend most time there (90/10 rule).

---

## 8. Data-Flow Analysis

Framework: for each basic block B compute `IN[B]` and `OUT[B]` with transfer function `OUT[B] = gen[B] ∪ (IN[B] − kill[B])` (for forward problems), with a meet operator at joins; solved by **iteration until fixed point** (monotone framework over a finite lattice, so it terminates). Initialisation and direction matter.

| Analysis | Direction | Meet | Equations | Used for |
| --- | --- | --- | --- | --- |
| **Reaching definitions** | Forward | ∪ (may) | `OUT = gen ∪ (IN − kill)`; `IN = ∪ OUT[pred]`; init OUT = ∅ | constant/copy propagation, def-use chains |
| **Available expressions** | Forward | ∩ (must) | `OUT = e_gen ∪ (IN − e_kill)`; `IN = ∩ OUT[pred]`; IN\[entry\] = ∅; others init to **universal set** | **global common subexpression elimination** |
| **Live variables** | **Backward** | ∪ (may) | `IN[B] = use[B] ∪ (OUT[B] − def[B])`; `OUT[B] = ∪ IN[succ]`; init IN = ∅ | dead code elimination, **register allocation** |
| Very busy (anticipable) expressions | Backward | ∩ | like available but backward | code hoisting |
| **Constant propagation** | Forward | lattice meet | see below | replace variables by constants |

- **Liveness details**: a variable is **live** at a point if its current value may be used later along some path. `use[B]` = variables read in B before any definition in B; `def[B]` = variables defined in B. At the end of the program nothing is live (OUT\[exit\] = ∅). Live range overlap → conflict in the **interference graph**; register allocation = **graph colouring** with k registers (spill if not k-colourable). Statement-level liveness: go backward through the block: live-in = (live-out − def) ∪ use.
- **Reaching definitions**: a definition d of x **reaches** a point if there is a path from d to the point with no other definition of x (killed). Forward, union. **gen\[B\]** = last definition of each variable in B; **kill\[B\]** = all other definitions of those variables.
- **Available expressions**: expression `x op y` is available at a point if it is computed on **every** path and neither x nor y redefined since. Must analysis: intersection at joins, so initialise non-entry IN/OUT to the universal set (otherwise results are too pessimistic).
- **Constant propagation**: lattice per variable: **⊤ (undefined/unknown yet) → constants c → ⊥/NAC (not a constant)**. Meet: `c ∧ c = c`, `c₁ ∧ c₂ = NAC` (c₁ ≠ c₂), `⊤ ∧ c = c`, `NAC ∧ x = NAC`. Transfer for `x = y + z`: constant if both operands constant, NAC if any NAC, ⊤ otherwise. The framework is **not distributive**: example, two paths with (a=1, b=2) and (a=2, b=1) then `c = a + b`; each path gives c = 3 but at the join a = NAC, b = NAC so the analysis (MFP) says c = NAC — loses precision, while MOP (meet over all paths) says 3.
- **Meet-over-paths (MOP)** ≥ **maximum fixed point (MFP)** in precision; they are equal for distributive frameworks (reaching defs, available expressions, live variables are distributive/bit-vector; constant propagation is not).
- **Efficiency**: bit-vector representation; visiting blocks in **depth-first order** (reverse postorder for forward problems, postorder for backward) converges quickly; for reducible graphs the number of passes ≤ loop connectedness + 2.
- **Which analysis for which optimisation**: global CSE → available expressions; dead-code elimination, register allocation → liveness; copy/constant propagation → reaching defs/constant propagation lattice; loop-invariant code motion → reaching defs + dominators.

### Worked mini-example (liveness)

```
B1: a = 1; b = 2
B2: c = a + b; if c > 5 goto B4
B3: d = c * 2 ... goto B5
B4: d = a ...
B5: print d
```

Backward: IN\[B5\] = {d}; OUT\[B3\] = OUT\[B4\] = {d}; IN\[B4\] = {a} (d defined before use); IN\[B3\] = {c}; OUT\[B2\] = IN\[B3\] ∪ IN\[B4\] = {c, a}; IN\[B2\] = (OUT − {c}) ∪ {a, b} = {a, b}; OUT\[B1\] = {a, b}; IN\[B1\] = ∅. Hence `b` is dead after B2 (not live in B3/B4), `a` is live until B4.

---

## 9. Code Generation (back end basics)

- Tasks: **instruction selection**, **register allocation and assignment**, **instruction ordering**. Target machine models: three-address machines, stack machines.
- **Register allocation** by graph colouring (Chaitin): build interference graph from liveness, simplify nodes with degree \< k, spill if stuck. Local allocation by usage counts.
- **Sethi–Ullman** labelling for the minimum number of registers to evaluate an expression tree without spilling: label(leaf) = 1 if it is a left child (needs a register), 0 if a right child operand can be used from memory; for an internal node with child labels l₁, l₂: label = `max(l₁, l₂)` if l₁ ≠ l₂, else `l₁ + 1`. (When every leaf needs a register, label leaf = 1.)
- **Code-generation order**: evaluate the child needing more registers first.
- **Instruction selection** by tree pattern matching; addressing modes reduce instruction count.
- **Memory aspects**: register descriptors and address descriptors track where each value lives.

---

## 10. High-Yield Traps and Shortcuts

1. **FIRST/FOLLOW**: add `$` to FOLLOW(start symbol); never put ε in FOLLOW; when `β ⇒* ε`, include FOLLOW of the head. Compute iteratively until no change.
2. **LL(1)**: check three conditions; left-recursive or ambiguous grammars are never LL(1). Common-prefix productions fail the FIRST-disjoint condition.
3. **LR hierarchy**: LR(0) ⊂ SLR ⊂ LALR ⊂ CLR. LALR has the same states as SLR/LR(0). LALR can add reduce/reduce conflicts only. Ambiguous grammars are never LR(1).
4. **SLR vs LR(1) conflicts**: the SLR table reduces on all of FOLLOW(A), so conflicts appear when FOLLOW(A) overlaps with a shift symbol.
5. **Number of LR(0) items** = Σ(|RHS| + 1) over productions (ε-production counts 1); then count states by building closure/goto.
6. In a bottom-up parse, **shifts = tokens**, **reductions = interior nodes**; the parse is the reverse of a rightmost derivation.
7. **Associativity/precedence** in grammars: left recursion → left assoc; right recursion → right assoc; operator nearer the start symbol has lower precedence.
8. **S-attributed ⊂ L-attributed**; inherited attributes from the right sibling make a definition non-L-attributed. S-attributed ⇒ bottom-up in one pass.
9. **SDT output questions**: determine when each action runs (position in RHS) and build the traversal order; for postfix SDT the outputs come in postorder.
10. **Parameter passing**: draw a table of variable values; copy-restore ≠ reference when aliasing or globals; call by name re-evaluates the actual expression each time.
11. **Static vs dynamic scope** programs: follow either textual nesting (static) or call chain (dynamic).
12. **Activation records**: control link (caller), access link (lexical parent), no static link is needed in languages without nested procedures (C).
13. **TAC counting**: number of temporaries/instructions for an expression; with DAG-based CSE the count is less. Quadruples vs triples vs indirect triples: the latter two avoid explicit result names; triples are hard to reorder.
14. **Leaders** give basic blocks: first instruction, jump target, instruction after a jump. Count carefully including unconditional jumps' successors.
15. **Data-flow direction/meet**: reaching defs (forward, ∪), available expr (forward, ∩), liveness (backward, ∪). Initialising available expressions with ∅ everywhere is a classic mistake (use the universal set for non-entry nodes).
16. **Constant propagation is not distributive**; MOP vs MFP difference arises only for non-distributive frameworks.
17. **Dead code**: an assignment whose variable isn't live after it. Not the same as unreachable code.
18. **Peephole** works on target code through a window; local optimisation works inside a basic block; global optimisation needs data-flow analysis across blocks.
19. **Lexer vs parser**: lexical analysis recognises only regular patterns; balanced parentheses/nesting belong to the parser.
20. **Garbage collection**: reference counting fails on cycles; mark-sweep reclaims cycles.

---

## 11. Practice Plan

1. **PYQs topic-wise (GATE CSE 2010–2026)**: (a) FIRST/FOLLOW and LL(1) tables, (b) LR(0)/SLR/LALR/CLR item sets and conflicts, (c) SDT and attribute questions, (d) three-address code, basic blocks, DAG counts, (e) data-flow analysis problems, (f) runtime/parameter passing/scope.
2. Hand-build the LR(0) automaton for 2–3 small grammars and mark conflicts; then re-check with SLR/LALR/CLR logic.
3. For every data-flow problem write gen/kill (or use/def) first, then iterate until stable; check one pass more than needed.
4. Keep an **error log** (concept + trap) and re-read only that in the last two weeks.
5. Memorise sections 3.3 to 3.5 (conditions and hierarchy), the data-flow table (section 8) and the traps (section 10).
6. Take full-length mocks; Compiler Design questions are short and rule-based, so speed and accuracy here are cheap marks.
