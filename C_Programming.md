# GATE 2027 – Programming in C and Recursion: Complete Revision Notes

Syllabus line: *Programming in C. Recursion. (Arrays, stacks, queues, linked lists, trees, BST, heaps and graphs are in the Data Structures notes.)*

C questions in GATE are **output-tracing and "what does this function do"** questions, usually 5 to 8 marks. They are won by knowing the exact rules below and by tracing carefully on paper.

**Default assumptions** (unless the question says otherwise): 32-bit `int` (4 bytes), `char` 1, `short` 2, `long` 4 or 8, `long long` 8, `float` 4, `double` 8, pointers 4 bytes (32-bit) or 8 (64-bit); two's complement; little-endian; `char` is signed.

---

## 1. Data Types, Constants, Conversions

| Type | Size | Range (typical) |
| --- | --- | --- |
| char / unsigned char | 1 | −128..127 / 0..255 |
| short / unsigned short | 2 | −32768..32767 / 0..65535 |
| int / unsigned int | 4 | −2³¹..2³¹−1 / 0..2³²−1 |
| long long | 8 | −2⁶³..2⁶³−1 |
| float / double | 4 / 8 | \~7 / \~15–16 decimal digits |

- Signed n-bit range: −2ⁿ⁻¹ … 2ⁿ⁻¹ − 1. **Unsigned overflow wraps modulo 2ⁿ** (well-defined). **Signed overflow is undefined behaviour** (UB) though usually wraps.
- `char c = 200;` (signed char) stores −56. `unsigned char c = 300;` stores 44 (300 mod 256).
- Character constants are `int` in C: `sizeof('a') == 4` (in C++ it is 1). `'a'` = 97, `'A'` = 65, `'0'` = 48, `'\0'` = 0; `'a' − 'A'` = 32.
- **Integer promotion**: `char`/`short` become `int` in expressions. **Usual arithmetic conversion** (rank): int → unsigned int → long → unsigned long → float → double → long double.
- **Signed vs unsigned comparison**: signed operand is converted to unsigned. `-1 > 1u` is **true** (−1 becomes 4294967295). `sizeof` returns `size_t` (unsigned), so `-1 < sizeof(int)` is **false**.
- Integer division truncates toward zero: `-7/2 = -3`, `-7%2 = -1`, `7%-2 = 1` (sign of `%` follows the dividend). `5/2 = 2`, `5/2.0 = 2.5`, `float f = 1/2;` gives 0.
- Float → int conversion truncates (3.9 → 3, −3.9 → −3). `0.1 + 0.2 != 0.3`; `0.1f == 0.1` is false (float vs double precision); never compare floats with `==`.
- Division by zero: integer → crash/UB; floating → `inf`/`nan`.
- Implicit narrowing on assignment: `int x = 3.99;` → 3.
- Octal literal starts with `0` (`010` = 8); hex `0x`. `1e3` is a double.

---

## 2. Operators, Precedence, Evaluation Order

**Precedence (high → low)**: `() [] -> .` > postfix `++ --` > unary (`! ~ + - ++ -- * & sizeof (cast)`, right-to-left) > `* / %` > `+ -` > `<< >>` > `< <= > >=` > `== !=` > `&` > `^` > `|` > `&&` > `||` > `?:` (right-to-left) > assignments (right-to-left) > `,`.

- Famous trap: `==` binds **tighter** than `&`, `^`, `|`: `a & b == c` means `a & (b == c)`.
- `*p++` = `*(p++)` (value at p, then pointer advances); `(*p)++` increments the pointee; `*++p` advances then dereferences; `++*p` increments the pointee first.
- `a<b<c` is `(a<b)<c` (compares 0/1 with c): `5>3>1` → `(1)>1` → 0.
- Relational and logical operators yield `int` 0 or 1. `!5 = 0`, `!!5 = 1`, `!0 = 1`.
- **Short-circuit**: `&&` skips the right side if left is 0; `||` skips if left is non-zero. Side effects on the right side may not happen. The `&&`, `||`, `?:` and `,` operators have a **sequence point**.
- **Comma operator**: `x = (1, 2, 3);` → 3. `x = 1, 2;` → x = 1.
- `if (a = 5)` is assignment (always true); `if (a = 0)` always false. A stray `;` after `if`, `for`, `while` makes an empty body.
- `sizeof` is a compile-time operator; its operand is **not evaluated**: `sizeof(i++)` does not change `i`. `sizeof(array)` gives the full size only where the array is visible (not for array parameters, which decay to pointers).
- Compound assignment: `x *= 2 + 3` = `x = x * (2 + 3)`.
- **Undefined / unspecified behaviour** (answer "undefined" if the option exists):
  - modifying a variable twice, or reading and modifying it, between sequence points: `i = i++`, `i++ + ++i`, `a[i] = i++`, `printf("%d %d", i, i++)`
  - order of evaluation of function arguments and of operands of `+`, `*`, `==` etc. is **unspecified**
  - signed overflow, null/dangling pointer dereference, array out of bounds, uninitialised read, shift by ≥ width or negative, division by zero, modifying a string literal, returning address of a local variable, wrong `printf` format specifier.

### Bitwise operators

- `& | ^ ~ << >>`. `~x = −x − 1` (`~0 = −1`, `~5 = −6`). `5&3 = 1`, `5|3 = 7`, `5^3 = 6`.
- `x << k` = x × 2ᵏ (if no overflow); `x >> k` = ⌊x / 2ᵏ⌋ for unsigned; right shift of a negative signed number is implementation-defined (usually arithmetic: `-8 >> 1 = -4`).
- **Bit tricks (high yield)**:
  - set bit k: `x | (1<<k)`; clear: `x & ~(1<<k)`; toggle: `x ^ (1<<k)`; test: `(x>>k) & 1`
  - **`x & (x−1)`** clears the lowest set bit → loop `while(x){x &= x-1; c++;}` counts set bits in as many iterations as there are 1s
  - `x & (x−1) == 0` (for x > 0) ⇔ x is a power of 2 (mind precedence; parenthesise)
  - `x & −x` isolates the lowest set bit; `x ^ x = 0`; `x ^ 0 = x`
  - swap without temp: `a ^= b; b ^= a; a ^= b;` (breaks if both names refer to the same variable)
  - even/odd: `x & 1`; multiply by 2ᵏ: shift; `x % 2ᵏ` = `x & (2ᵏ − 1)` for non-negative x.

---

## 3. Control Flow

- `switch`: the expression must be an integer type; case labels are distinct integer constants; **fall-through** continues into the next case until `break`; `default` can appear anywhere and executes when no case matches (and falls through if no break).
- `break` leaves the innermost loop/switch; `continue` goes to the next iteration (in `for`, executes the increment; in `do-while` jumps to the condition check). `goto` jumps within a function.
- `while(i--)` runs while the old value is non-zero: for i = 5 it runs 5 times, and `i` ends as −1. `for(;;)` is infinite. `do { } while()` runs at least once.
- Loop with **unsigned counter**: `for (unsigned i = 5; i >= 0; i--)` is infinite (always ≥ 0).
- Dangling else: `else` binds to the nearest unmatched `if`.
- `for(i=0; i<n; i++);` followed by a statement → the statement runs once after the loop.

---

## 4. Storage Classes, Scope, Lifetime

| Class | Storage | Default init | Scope | Lifetime |
| --- | --- | --- | --- | --- |
| `auto` (local) | Stack | **Garbage** | Block | Block execution |
| `register` | CPU register (request) | Garbage | Block | Block; **cannot take `&`** |
| `static` (local) | Data/BSS | **0** | Block | **Whole program**; initialised **once** |
| `static` (global) | Data/BSS | 0 | File (internal linkage) | Whole program |
| `extern` / global | Data/BSS | 0 | Program (external linkage) | Whole program |

- A `static` local keeps its value between calls (typical counter question); its initialiser must be a constant expression and executes only once.
- **Block scope & shadowing**: an inner declaration hides the outer one with the same name; a local variable hides a global. Variable declared in a loop body is recreated each iteration (`auto`).
- `extern int x;` is a declaration (no storage); a definition allocates.
- **Memory layout**: Text (code, read-only) → Initialised data → BSS (uninitialised/zero globals and statics) → Heap (grows up, `malloc`) → ... → Stack (grows down; locals, parameters, return addresses). String literals live in read-only memory.
- Compilation pipeline: preprocessor → compiler → assembler → linker → loader.

---

## 5. Functions and Parameter Passing

- C is **call by value** only. To modify the caller's variable pass its address. A `swap(int a, int b)` without pointers does nothing.
- Arrays are passed as **pointers to the first element** (`void f(int a[])` ≡ `void f(int *a)`; `sizeof(a)` inside is the pointer size). Hence modifications to elements are visible to the caller.
- Passing a 2-D array needs all dimensions except the first: `void f(int a[][4])`.
- Passing a struct by value copies the entire struct.
- Returning a pointer to a local (auto) variable is a dangling pointer (UB); returning a pointer to a `static` or heap object is fine.
- `void f()` (unspecified parameters, old style) vs `void f(void)` (none). `main(int argc, char *argv[])`: `argc` includes the program name; `argv[0]` is the program name; `argv[argc]` is NULL.
- Function pointers: `int (*fp)(int, int) = add; fp(2,3)` or `(*fp)(2,3)`. Array of function pointers; callbacks (`qsort`).
- Order of evaluation of function arguments is unspecified (gcc often evaluates right-to-left, giving surprising output; treat as undefined unless the problem says otherwise).
- Variable-argument functions use `stdarg.h` (`va_list`, `va_start`, `va_arg`, `va_end`); rare in GATE.

---

## 6. Pointers

- `p + k` advances by `k × sizeof(*p)` bytes; `p − q` (same array) gives the number of **elements** between them; pointer comparison valid only within the same array. `void *` arithmetic isn't standard (gcc treats it as 1 byte). Pointers cannot be added to each other.
- `int *p, q;` declares `q` as an **int**, not a pointer.
- Const rules: `const int *p` (data constant), `int * const p` (pointer constant), `const int * const p` (both). Read declarations right to left.
- `int *p[5]` → array of 5 pointers; `int (*p)[5]` → pointer to an array of 5 ints; `int **pp` → pointer to pointer.
- **Arrays and pointers**: `a[i] ≡ *(a+i) ≡ *(i+a) ≡ i[a]`. For `int a[3][4]`: `a[i][j] ≡ *(*(a+i)+j)`; `a+1` moves a whole row (16 bytes); `*(a+1)` is the 2nd row (an array decaying to `int*`).
- Array name is a non-modifiable address: `a++` is illegal; `p++` is fine. `&a` has type "pointer to whole array": `&a + 1` jumps by `sizeof(a)`, while `a + 1` jumps by one element.
- `sizeof(a)/sizeof(a[0])` = number of elements (only where `a` is a true array).
- Uninitialised ("wild") pointers, NULL dereference, dangling pointers (after `free` or after the scope ends) → UB.
- Pointer to pointer is needed to modify a pointer in a function (e.g. head of a linked list: `void insert(struct node **head, int x)`).
- Casting: `*(int*)&f` reinterprets bytes. `(char*)&x` reads the first byte of an int: on a **little-endian** machine `int x = 0x12345678; *(char*)&x` is 0x78. Endianness is what union/pointer-cast questions test.
- `p = &a[2]; p[-1]` is valid (a\[1\]). Pointer to a struct member: `p->m` ≡ `(*p).m`.

---

## 7. Arrays and Strings

- Indices start at 0; **no bounds checking**. `int a[5] = {1};` → {1,0,0,0,0}. `int a[] = {1,2,3};` size 3. Global/static arrays default to 0; local arrays are garbage.
- 2-D arrays are **row-major**: address of `a[i][j]` = base + (i × cols + j) × size.
- `char s[] = "abc";` → modifiable array of **4** bytes (includes `'\0'`). `char *s = "abc";` → pointer to a read-only literal; **modifying it is UB**. `sizeof("abc") = 4`, `strlen("abc") = 3`. A `char` array without room for `'\0'` isn't a valid string.
- Comparing strings with `==` compares **addresses**, not contents; use `strcmp` (returns \<0, 0, >0). Two identical string literals may or may not share an address.
- `<string.h>`: `strlen`, `strcpy`, `strncpy`, `strcat`, `strcmp`, `strchr`, `strstr`, `memcpy`, `memset`. `strcpy` doesn't check bounds (buffer overflow). `gets` is unsafe; use `fgets`.
- `printf("%s", s)` prints until `'\0'`. `printf("%c", 65)` prints `A`. `printf("%d", 'A')` prints 65. Character arithmetic: `'a' + 1 = 98`; `(char)('a' + 1) = 'b'`; `s[i] − '0'` converts a digit char to int.
- `scanf("%d", &x)` returns the number of items read (EOF = −1 at end); `scanf("%s", s)` stops at whitespace; the newline remains in the buffer for the next `getchar()`/`%c`.

---

## 8. Structures, Unions, Enums, typedef

- **Structure**: members stored in order with **padding** for alignment (each member aligned to its size; total padded to a multiple of the largest alignment). Example: `struct {char a; int b; char c;}` → 12 bytes; reordered `{int b; char a; char c;}` → 8 bytes. A pointer member does not add its target's size.
- **Union**: all members share the same memory; size = largest member (padded). Writing one member and reading another reinterprets the bytes (endianness matters).
- A struct can contain a pointer to its own type (self-referential), not itself. Assignment `s1 = s2` copies the whole struct (shallow copy for pointers). Struct comparison with `==` is not allowed.
- **Bit fields**: `unsigned a : 3;` (3-bit field).
- **enum**: constants start at 0 and increment by 1 unless set (`enum {A, B=5, C}` → 0, 5, 6); they are `int`.
- `typedef` creates an alias; it is not a macro. `typedef struct node {...} Node;`.
- `offsetof`, `sizeof(struct)` include padding; array of structs: `a[i]` address = base + i × sizeof(struct).

---

## 9. Dynamic Memory

- `malloc(n)` → uninitialised block of n bytes (returns `void *`, NULL on failure); `calloc(n, size)` → zero-initialised; `realloc(p, n)` may **move** the block (use the returned pointer); `free(p)` releases it. `free(NULL)` is safe; **double free**, freeing a non-heap pointer, or using memory after free is UB.
- **Memory leak**: losing the only pointer to a heap block without `free`. **Dangling pointer**: pointer to freed/out-of-scope memory.
- Size expression: `malloc(n * sizeof(int))`; `malloc(sizeof(p))` is wrong (size of the pointer, not the object); correct is `malloc(sizeof(*p))`.
- 2-D dynamic array: `int **a = malloc(r * sizeof(int*)); a[i] = malloc(c * sizeof(int));`.
- Heap vs stack: heap persists until freed, slower, fragmentation; stack automatic, fast, limited size (deep recursion → stack overflow).

---

## 10. Preprocessor

- Macros are **textual substitution** before compilation; no type checking.
- `#define SQ(x) x*x` → `SQ(2+3)` = `2+3*2+3` = **11**, not 25. Fix: `#define SQ(x) ((x)*(x))`.
- Macro arguments with side effects are evaluated multiple times: `#define MAX(a,b) ((a)>(b)?(a):(b))`, `MAX(i++, j++)` increments the larger one twice.
- `#define` constants have no storage and no type; `const` variables do.
- Stringizing `#x` and token pasting `a##b`; conditional compilation `#ifdef / #ifndef / #if / #else / #endif`; include guards.
- Macro definitions end without a semicolon; a trailing `;` becomes part of the replacement text.

---

## 11. printf / scanf

- `printf` returns the number of characters printed: `printf("%d", printf("ab"))` prints `ab2`.
- Formats: `%d` int, `%u` unsigned, `%ld` long, `%lld`, `%f` float/double, `%lf` (scanf for double), `%c`, `%s`, `%x` hex, `%o` octal, `%e`, `%p` pointer, `%%` percent. Width/precision: `%5d`, `%-5d`, `%05d`, `%.2f`, `%*d`.
- `printf("%d", 3.5)` or `%f` with an int → UB (garbage). `printf("%s", 65)` crashes.
- `%d` with a `char` prints its numeric (ASCII) value after promotion. Unsigned printed with `%d` shows negative numbers when the top bit is 1.
- `printf` arguments: unspecified evaluation order.

---

## 12. Recursion

- A recursive function needs a **base case** and progress toward it. Each call pushes an **activation record** (return address, parameters, locals) → **stack space = O(depth)**. Without a base case → stack overflow.
- **Order of output**: statements *before* the recursive call execute on the way down (n, n−1, …), statements *after* execute while unwinding (…, n−1, n). Tracing technique: write the call tree with values and returns.
- **Tail recursion**: the recursive call is the last operation (can be optimised into a loop, O(1) stack). `fact(n) = n * fact(n−1)` is **not** tail recursive.
- Static/global variables retain values across recursive calls; local variables are per-call.
- Recursion with pointers: parameters are copies of addresses, so changes via pointers persist.
- **Direct vs indirect (mutual) recursion** (`isEven` ↔ `isOdd`).

### Classic recurrences and facts

| Function | Facts |
| --- | --- |
| `fact(n) = n*fact(n-1)` | n calls (depth n), O(n) time, O(n) stack |
| `fib(n) = fib(n-1)+fib(n-2)` (naive; base fib(0)=0, fib(1)=1) | **calls = 2·F(n+1) − 1**; additions = F(n+1) − 1; exponential time O(φⁿ); stack depth n. With memoisation: O(n) |
| Tower of Hanoi | moves = 2ⁿ − 1; calls = 2ⁿ⁺¹ − 1 including base calls |
| `gcd(a,b) = b==0 ? a : gcd(b, a%b)` | O(log min(a,b)) steps; worst case consecutive Fibonacci numbers |
| `power(x,n)` halving | O(log n) multiplications; `x*power(x,n-1)` is O(n) |
| Binary search (recursive) | O(log n) time, O(log n) stack |
| Sum of digits / reverse digits / palindrome | depth = number of digits |
| `f(n) = n>100 ? n-10 : f(f(n+11))` (McCarthy 91) | returns 91 for all n ≤ 100, n − 10 for n > 100 |
| Ackermann `A(m,n)` | `A(0,n)=n+1`, `A(m,0)=A(m-1,1)`, `A(m,n)=A(m-1,A(m,n-1))`; `A(1,n)=n+2`, `A(2,n)=2n+3`, `A(3,n)=2ⁿ⁺³−3`; not primitive recursive |

- To find **how many times a statement executes**, write the recurrence and solve it (see Algorithms notes: Master theorem). Counting calls: C(n) = C(n−1) + C(n−2) + 1.
- Recursive functions on trees/lists: preorder prints before recursion, inorder between, postorder after; recursive list reverse; recursive binary search.
- Typical output questions: `void f(int n){ if(n==0) return; printf("%d",n); f(n-1); printf("%d",n);}` with n = 3 → 321123.
- Mutual recursion and recursion with array modification (e.g. recursive bubble sort, array reversal): trace index by index.
- Recursion vs iteration: both can compute the same functions; recursion costs stack space; any recursion can be converted using an explicit stack.

---

## 13. Linked List and Tree Code Patterns (as C questions)

- Node: `struct node { int data; struct node *next; };`. Traversal: `while(p){...; p = p->next;}`.
- Insert at head needs the new head returned or `struct node **head`. Deleting a node when only a pointer to it is given: copy next's data, remove next (not possible for the last node).
- Typical question functions: reverse a list (three pointers), find the middle (slow/fast), detect a cycle, merge two sorted lists, count nodes, remove duplicates, swap pairs, tree height (`1 + max(h(l), h(r))`, empty = −1 or 0 as defined), number of leaves, mirror/inorder successor.
- **Method of tracing**: draw boxes and arrows, execute statements one by one, update the diagram; watch for NULL dereference on `p->next->next`.

---

## 14. Classic Output Snippets (know these answers)

| Code | Result |
| --- | --- |
| `printf("%d", sizeof('a'))` | 4 |
| `char *s = "hello"; printf("%d %d", strlen(s), sizeof(s));` | 5 and pointer size (4 or 8) |
| `char s[] = "hello"; sizeof(s)` | 6 |
| `int x = 5; printf("%d", x++ + ++x);` | undefined |
| `printf("%d", printf("ab"));` | `ab2` |
| `#define SQ(x) x*x` ; `SQ(2+3)` | 11 |
| `unsigned u = 1; if (-1 > u)` | true |
| `-7/2`, `-7%2` | −3, −1 |
| `5 & 3`, `5 \| 3`, `5 ^ 3`, `~5`, `5 << 2`, `-8 >> 1` | 1, 7, 6, −6, 20, −4 |
| `int a[] = {1,2,3}; printf("%d", *(a+1));` | 2 |
| `int a[5]; sizeof(a)/sizeof(a[0])` | 5 |
| `static int c = 0; c++;` in a function called 3 times | c = 3 |
| `int i = 3; while(i--) printf("%d", i);` | 210 |
| `char c = 255; (c == 255)` with signed char | false (c = −1) |
| `if (x = 5)` | always true, x becomes 5 |
| `0.1 + 0.2 == 0.3` | false |
| `float f = 1/2;` | 0.0 |
| `int x = 010;` | 8 |
| `printf("%d", 5>3>1);` | 0 |
| `a & b == c` | `a & (b == c)` |
| `switch(1){case 1: printf("a"); case 2: printf("b"); default: printf("c");}` | abc |
| `x = (1,2,3)` | 3 |

---

## 15. High-Yield Traps and Shortcuts

1. **Always check for UB**: `i++` twice in one expression, argument evaluation order, modifying literals, out-of-bounds, uninitialised variables. If an option says "undefined / compiler dependent", it's usually correct.
2. **Precedence of `*p++`, `(*p)++`, `++*p`**: write a small table of what changes (pointer or pointee) and what is returned.
3. **`sizeof`**: type and size depend on the context (array vs decayed pointer, `'a'` is int, struct padding).
4. **Signed/unsigned mix** in comparisons and loops; negative `char` values; shifting negative numbers.
5. **Macros**: expand textually before evaluating; check parentheses and side effects.
6. **Short-circuit evaluation**: side effects in the skipped operand never happen; `&&` has higher precedence than `||`.
7. **Static variables** are initialised once and persist; in recursion they are shared by all calls; automatic variables are separate per call.
8. **String literal vs array**: `char *s` literal is read-only; `char s[]` is a copy; `==` compares addresses.
9. **Switch fall-through** and `default` placement; `continue` inside `switch` inside a loop applies to the loop.
10. **Pointer arithmetic** scales by type size; subtraction returns the element count; `a+1` vs `&a+1`.
11. **Pass by value**: functions can't change caller variables unless passed pointers; pointer parameters can be changed locally without affecting the caller (use `**`).
12. **Recursion output**: separate "before call" and "after call" prints; draw the call tree; count calls with recurrences; naive Fibonacci is exponential.
13. **Struct size** = members with alignment padding + tail padding; union size = largest member.
14. **Float comparison / integer division** surprises: `1/2 = 0`, `3/2*2.0 = 2.0`, but `3/2.0*2 = 3.0`.
15. **Linked list code**: look for lost nodes (memory leak), NULL checks, correct order of pointer updates (save `next` before changing it).
16. `printf` returns the number of characters printed; `scanf` returns the number of successfully read items.
17. `main` without return gives 0 in C99; `exit` flushes buffers; unflushed `printf` buffers are duplicated on `fork` (see OS notes).
18. **Time/space complexity of code**: loop counts (log n for doubling, n log n for nested harmonic loops); recursion depth sets the stack space.
19. Operations on `char`: `c - 'a'` gives a 0–25 index; `toupper` ≈ `c - 32` for lowercase letters.
20. Arrays can't be assigned (`a = b` illegal) but structs containing arrays can (member-wise copy).

---

## 16. Practice Plan

1. **PYQs (GATE CSE 2010–2026) on C**: pointers, recursion, output tracing, macros, structs. Solve at least 3 to 4 questions daily, **writing the memory diagram and call tree on paper**.
2. Compile and run doubtful snippets (on your own machine) *after* predicting the output; note every mismatch in an **error log** with the rule you missed.
3. Revise sections 14 and 15 of these notes the day before each mock.
4. Be able to write: list reverse, middle-node, binary search (iterative and recursive), GCD, power, bit-count, string reverse, strcmp, fib with memo, Hanoi, tree traversals.
5. Practise code with static/global variables inside recursion; this is the most common source of wrong answers.
6. Combine with the Data Structures and Algorithms notes: many "C" questions in GATE are really DS/Algo questions written in C.
