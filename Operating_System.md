# GATE 2027 – Operating Systems: Complete Revision Notes

Syllabus covered: system calls, processes, threads, IPC, concurrency and synchronization, deadlock, CPU and I/O scheduling, memory management and virtual memory, file systems. Read once, then practise PYQs (OS is numerical-heavy; most marks come from scheduling, paging and synchronization).

---

## 1. Basics, System Calls, Modes

- OS = resource manager + extended machine. Kernel mode (privileged instructions) vs user mode; the **mode bit** switches on trap/interrupt/system call.
- **Privileged instructions**: I/O instructions, set timer, clear memory, modify mode bit, disable interrupts, load base/limit registers. Reading the clock, `trap`, and ordinary arithmetic are non-privileged.
- **System call**: user→kernel request through a **trap** (software interrupt). Parameters passed via registers, stack or a memory block. Examples: `fork, exec, wait, exit, open, read, write, close, pipe, kill`.
- **Interrupt** (asynchronous, from hardware) vs **trap/exception** (synchronous, from the running instruction, e.g. divide by zero, page fault, system call). After an interrupt, the ISR runs, then control returns.
- Types of kernel: monolithic (fast, big), microkernel (small, IPC overhead), hybrid, layered, modular.
- Boot: BIOS/UEFI → bootloader → kernel. **Dual-mode** protects the OS from user programs; timer prevents infinite loops.
- Batch, multiprogramming (increases CPU utilisation), time sharing (multitasking, response time), real-time (hard/soft deadlines), multiprocessor (SMP/AMP), distributed.

---

## 2. Processes

- **Process** = program in execution: text, data, heap, stack + PCB. **PCB** holds PID, state, PC, registers, scheduling info, memory info, I/O status, accounting.
- **States**: New → Ready → Running → Terminated; Running → Waiting (I/O) → Ready; Running → Ready (preemption); suspended-ready and suspended-wait when swapped out (medium-term scheduler).
- **Schedulers**: long-term (job scheduler; controls degree of multiprogramming), short-term (CPU scheduler; runs most frequently), medium-term (swapping).
- **Context switch**: save state of the old process in its PCB, load the new one. Pure overhead; time depends on hardware (registers, TLB flush, cache). Dispatcher latency = time to stop one process and start another.
- **I/O-bound** vs **CPU-bound** mix gives the best utilisation.

### fork(), exec(), wait()

- `fork()` creates a child (copy of the parent address space, copy-on-write); returns **0 to child**, **child PID to parent**, −1 on failure.
- **Counting**: n successive `fork()` calls (not in conditionals) → 2ⁿ processes total, 2ⁿ − 1 child processes. Fork inside `if(fork())` / `&&` / `||` conditions changes counts; trace the tree carefully.
- `exec()` replaces the process image (same PID). `wait()` blocks parent until a child terminates. **Zombie** = terminated but parent hasn't waited. **Orphan** = parent died; adopted by init.
- Output questions: remember buffered `printf` (stdout line-buffered only for terminals) gets duplicated by fork if not flushed.

---

## 3. Threads

- Thread = unit of CPU execution; shares code, data, heap, open files with peer threads; has its own **PC, registers, stack, thread ID**.
- Benefits: responsiveness, resource sharing, economy (cheaper creation/switch), scalability on multiprocessors.
- **User-level threads**: managed by a library; fast switching, no kernel involvement; one blocking system call blocks the whole process; cannot use multiple CPUs. **Kernel-level threads**: kernel schedules them; slower but true parallelism and non-blocking for the process.
- Models: many-to-one, one-to-one (Linux, Windows), many-to-many.
- Multithreading consideration: if a process has a single-threaded CPU-bound section, extra threads don't speed it up. **Amdahl's law**: speedup = 1 / (S + (1−S)/N), S = serial fraction.

---

## 4. Inter-Process Communication (IPC)

- **Shared memory** (fast, needs synchronisation) and **message passing** (send/receive, blocking or non-blocking, direct/indirect).
- Pipes (unnamed: parent–child, unidirectional; named FIFOs), message queues, sockets, signals, semaphores, RPC.
- Buffering: zero capacity (rendezvous), bounded, unbounded.

---

## 5. CPU Scheduling

**Definitions**: Arrival time (AT), Burst time (BT), Completion time (CT), **Turnaround TAT = CT − AT**, **Waiting WT = TAT − BT**, **Response = first CPU − AT**. Throughput = processes/time. CPU utilisation = busy/total.

| Algorithm | Preemptive? | Notes |
| --- | --- | --- |
| FCFS | No | Convoy effect; simple; not optimal |
| SJF | No | **Minimum average waiting time** among non-preemptive; starvation possible |
| SRTF (preemptive SJF) | Yes | **Optimal average waiting time overall**; starvation of long jobs |
| Priority | Both | Starvation → **aging** |
| Round Robin | Yes | Time quantum q; q large → FCFS; q very small → overhead (context switches); typically 10–100 ms |
| Multilevel queue | – | Fixed queues (foreground/background) |
| MLFQ | Yes | Processes move between queues; prevents starvation |
| HRRN | No | Response ratio = (W + S)/S; avoids starvation |

- **Round Robin**: a newly arriving process enters the ready queue *before* the preempted one if they occur at the same instant (convention used in most GATE solutions; read the question). Number of context switches with n processes each needing k quanta ≈ n·k − 1.
- Max waiting time in RR with n processes: (n−1)q. Response time ≤ (n−1)q.
- In FCFS, average WT depends on arrival order; for SJF with all AT = 0, sort ascending burst.
- **Real-time**: Rate Monotonic (static priority: shorter period = higher priority; schedulable if Σ(Cᵢ/Pᵢ) ≤ n(2^(1/n) − 1), ≈ 0.693 for large n) and **EDF** (dynamic; schedulable iff Σ(Cᵢ/Pᵢ) ≤ 1).
- Process with I/O: add the I/O time into the timeline, CPU idle if nobody is ready. Gantt chart everything by hand.
- **Convoy effect**: short processes stuck behind a long one.

---

## 6. Process Synchronization

### Critical section

- Race condition when the outcome depends on execution order. Requirements: **mutual exclusion, progress, bounded waiting**.
- **Software solutions**: Peterson's (two processes; `flag[i]`, `turn`; satisfies all three), Dekker's; strict alternation violates progress; "lock variable" (plain test-then-set) violates mutual exclusion.
- **Hardware**: `Test-and-Set`, `Swap`, compare-and-swap (atomic); spinlocks use busy waiting (good for short waits on multiprocessors); disabling interrupts works only on uniprocessors.
- **Mutex** (ownership: the locker unlocks) vs **binary semaphore** (any process can signal). **Monitor**: high-level construct, only one process active inside; condition variables `wait/signal`.

### Semaphores

- Counting semaphore S: `wait(S)`/P: S−−, block if S \< 0; `signal(S)`/V: S++, wake one if S ≤ 0. Initial value = number of available resources.
- Semaphore value interpretation: if S = −k, k processes are blocked. **Final value questions**: n wait operations − m signals on initial value s → s − n + m (but remember blocked waits).
- Improper use: swapping wait/signal order → mutual exclusion failure or **deadlock** (e.g. `wait(mutex)` before `wait(empty)` in producer–consumer).

### Classic problems (know the semaphore setup)

- **Producer–Consumer (bounded buffer, size N)**: `mutex=1, empty=N, full=0`. Producer: `wait(empty); wait(mutex); add; signal(mutex); signal(full)`. Consumer: `wait(full); wait(mutex); remove; signal(mutex); signal(empty)`.
- **Readers–Writers**: first-readers-writers (reader priority, possible writer starvation) uses `mutex=1, wrt=1, readcount=0`. Writers-priority variants starve readers.
- **Dining Philosophers** (5 forks): naive "pick left then right" → deadlock. Fixes: at most 4 philosophers sit, pick both forks atomically, asymmetric (odd left-first, even right-first), or a monitor. Max philosophers that can eat concurrently = ⌊n/2⌋.
- **Sleeping Barber**, **Cigarette smokers**: less common.
- Barrier, ordering constraints (S1 before S2: `sem=0; S1; signal(sem) || wait(sem); S2`).
- Questions ask the **minimum/maximum value** of a shared variable after interleaved increments (e.g. n processes each doing `x = x + 1` k times without sync → min 2 (for n ≥ 2, tricky case), max n·k). Work out with the load–add–store interleaving.

### Other concepts

- **Priority inversion** (low-priority holds lock needed by high-priority): fix by priority inheritance.
- Starvation (indefinite wait) vs deadlock (circular wait).

---

## 7. Deadlock

- **Four necessary conditions** (all must hold): **mutual exclusion, hold and wait, no preemption, circular wait**.
- **Resource Allocation Graph (RAG)**: request edge P→R, assignment edge R→P. No cycle → no deadlock. Cycle with **single-instance** resources → deadlock (necessary and sufficient). Cycle with multi-instance resources → only **possible** deadlock.
- **Handling**: prevention (break one condition), avoidance (Banker's), detection + recovery, ignore (ostrich).
  - Break hold-and-wait: request everything at once; break circular wait: **resource ordering**; break no-preemption: preempt.
- **Banker's algorithm** (avoidance; needs max demand in advance): Need = Max − Allocation. Safety algorithm: find P with Need ≤ Work; Work += Allocation; repeat; safe if all finish (**safe sequence** may be non-unique). Safe state ⇒ no deadlock; unsafe state ⇒ *possible* deadlock.
  - Resource request: check Request ≤ Need, ≤ Available, pretend to allocate, run safety check.
  - Complexity O(m·n²).
- **Detection**: same as safety algorithm using current Request matrix; wait-for graph for single instance. Recovery: terminate processes (all / one at a time), preempt resources with rollback.
- **Minimum number of resources to guarantee no deadlock**: n processes, each needing at most m_i units: deadlock-free if total units R ≥ Σ(m_i − 1) + 1. For identical max m: R ≥ n(m−1) + 1 → equivalently, maximum n that can be deadlock-free with R units: n ≤ (R−1)/(m−1).
- Deadlock possible with **k** processes requesting resources in opposite orders; lock-ordering prevents it.

---

## 8. Memory Management

### Basics

- **Logical (virtual) address** vs **physical address**; MMU maps at run time. **Relocation register (base) + limit register** protect and relocate.
- Binding: compile time (absolute code), load time (relocatable), execution time (needs hardware).
- **Dynamic loading, dynamic linking, swapping, overlays.**

### Contiguous allocation

- Fixed partitions → **internal fragmentation**; variable partitions → **external fragmentation** (fixed by **compaction**, only with dynamic relocation).
- Placement: **first fit** (fast), **best fit** (smallest adequate hole; leaves tiny holes, slow), **worst fit** (largest hole). First fit and best fit are generally better than worst fit. **Next fit** starts from the last allocation.
- **Buddy system**: block sizes powers of 2; internal fragmentation; fast coalescing. A request of size s gets the next power of 2 ≥ s.
- **50% rule**: with first fit, for every N allocated blocks about 0.5N blocks are lost to fragmentation.

### Paging

- Logical memory divided into **pages**, physical into **frames** of the same size; no external fragmentation, small internal fragmentation (average half a page per process).
- Address: `logical address = page number (p) | offset (d)`. If logical address space is 2^m and page size 2^n, then p has m−n bits, d has n bits.
- Number of pages = LAS / page size; number of frames = PAS / frame size; **page table entries = number of pages**; size = entries × entry size. Entry includes frame number + valid/dirty/reference/protection bits.
- **Page table size** with a k-bit virtual address, page size 2^n, entry size e bytes: `2^(k−n) × e`.
- **Multilevel paging**: break the page table so that each level fits in one page. Bits per level = log₂(page size / entry size). Number of levels needed such that the top-level table fits in a page. **Inverted page table**: one entry per frame (size ∝ physical memory), needs hashing for lookup.
- **TLB** (associative cache of page table entries): hit ratio h, TLB access t, memory access m: `EMAT = h(t + m) + (1−h)(t + 2m)` for single-level paging; for k-level paging a miss costs `t + (k+1)m`. (If t is ignored: h·m + (1−h)·2m.)
- With page fault: `EAT = (1−p)·m + p·(page fault service time)`. Page fault service ≈ 8 ms vs memory access \~ 200 ns → even tiny p hurts.
- **Shared pages** (reentrant code), copy-on-write.

### Segmentation

- Logical address = (segment number s, offset d); segment table has base + limit; offset ≥ limit → trap. Supports user view, protection and sharing; causes **external fragmentation**. **Segmentation with paging** (e.g. Intel x86): segment → linear address → page.

### Virtual memory

- **Demand paging**: a page is brought in only when referenced; lazy swapper (pager). **Page fault steps**: trap → check valid → find free frame (or replace) → read page from disk → update page table → restart instruction.
- **Locality of reference** makes it work. **Thrashing**: more time paging than executing (degree of multiprogramming too high). Fixes: working-set model, page-fault-frequency control, lower multiprogramming. CPU utilisation falls when thrashing starts.
- **Working set** W(t, Δ): pages referenced in the last Δ references.

### Page replacement

| Algorithm | Notes |
| --- | --- |
| **FIFO** | Simple; **Belady's anomaly** (more frames → more faults) possible |
| **Optimal (OPT/MIN)** | Replace the page used farthest in the future; lowest faults; unrealisable; no anomaly |
| **LRU** | Replace least recently used; stack algorithm; no anomaly; needs hardware support |
| Second chance / Clock | Reference bit approximates LRU |
| LFU / MFU | Count-based |
| Enhanced second chance | (reference, modify) pairs: prefer (0,0) |

- **Stack algorithms** (OPT, LRU, LFU): the set of pages in n frames is a subset of those in n+1 frames → no Belady's anomaly. FIFO is not.
- Fault count: simulate by hand with the reference string; first references always fault (compulsory/cold faults). Minimum faults = number of **distinct pages** (for unlimited frames).
- Frame allocation: equal, proportional, priority; global vs local replacement.
- **Dirty (modify) bit**: avoids writing unmodified pages back.
- Page size trade-off: larger pages → smaller page table, more internal fragmentation, fewer page faults for sequential access; optimal page size ≈ √(2·s·e) (s = process size, e = entry size).

---

## 9. File Systems

- **File**: named collection of related information; attributes: name, type, location, size, protection, timestamps. Operations: create, open, read, write, seek, delete, truncate. **Open-file table** (system-wide and per-process). **File descriptor** is an index into the per-process table.
- **Access methods**: sequential, direct (random), indexed.
- **Directory structures**: single-level, two-level, tree, acyclic graph (links; hard link vs symbolic link), general graph (cycle problem; garbage collection).
  - **Hard link**: another directory entry to the same inode (same filesystem only, link count increments, file deleted when count = 0). **Symbolic link**: a file containing a path; can dangle; can cross file systems.

### Allocation methods

| Method | Pros | Cons |
| --- | --- | --- |
| **Contiguous** | Fast sequential and direct access; minimal seeks | External fragmentation; file growth hard |
| **Linked** | No external fragmentation; easy growth | No direct access; pointer overhead; reliability |
| **FAT** (linked via table) | Direct access using table (cached) | Table size: entries = number of blocks |
| **Indexed** | Direct access; no external fragmentation | Index block overhead; linked/multilevel/combined index for large files |

- **Unix inode**: 12 (or 10) direct, 1 single indirect, 1 double indirect, 1 triple indirect. With block size B and pointer size P: pointers per block = B/P. **Max file size** = B × (D + (B/P) + (B/P)² + (B/P)³). Example: B = 1 KB, P = 4 B → 256 pointers/block; with 10 direct: 10 KB + 256 KB + 64 MB + 16 GB.
- Number of blocks needed for a file of size F: ⌈F/B⌉; index blocks count must be added in indexed schemes. Disk block address size limits the number of addressable blocks (2^bits).
- **Free space management**: bit vector/bitmap (size = number of blocks bits), linked list, grouping, counting. Bitmap size = disk size / block size (bits).
- **Disk space**: internal fragmentation in the last block of each file; larger block → faster transfer but more wasted space.
- File system mounting, VFS layer, journalling (log-structured) for crash consistency, **ACL** and permission bits (Unix rwx for user/group/other: `chmod 755` = rwxr-xr-x).

---

## 10. I/O Systems and Disk Scheduling

- I/O techniques: **programmed I/O (polling)**, **interrupt-driven**, **DMA** (DMA controller transfers a block directly between device and memory; CPU interrupted once per block; **cycle stealing** shares the bus). DMA is best for large transfers.
- **Device drivers**, **spooling** (printers), buffering (single, double, circular), caching.
- **Disk structure**: platters, tracks, sectors, cylinders. **Disk access time = seek time + rotational latency + transfer time.**
  - Average rotational latency = half a rotation = 1/(2·RPM/60) seconds.
  - Transfer time = (bytes to transfer / bytes per track) × rotation time.
  - Disk capacity = surfaces × tracks per surface × sectors per track × bytes per sector.
- **Disk scheduling** (total head movement; simulate by hand):
  - **FCFS**: fair, high movement.
  - **SSTF**: shortest seek first; may starve far requests.
  - **SCAN (elevator)**: move to one end servicing requests, then reverse. **C-SCAN**: one direction only, jump back (more uniform waiting). **LOOK / C-LOOK**: go only as far as the last request (practical versions; GATE often means this when it says "SCAN" if the question says the head reverses at the last request — read carefully).
  - Seek time dominates; SSD has no seek.
- **RAID**: 0 (striping, no redundancy), 1 (mirroring), 5 (block-level striping with distributed parity; tolerates one disk failure; usable capacity (N−1) disks), 6 (two parity), 10 (mirror + stripe).

---

## 11. Quick Formula Sheet

| Item | Formula |
| --- | --- |
| TAT, WT | TAT = CT − AT; WT = TAT − BT |
| Pages | LAS / page size |
| Page table size | pages × entry size |
| Offset bits | log₂(page size) |
| EMAT (TLB) | h(t+m) + (1−h)(t+2m) |
| EAT (page fault) | (1−p)·m + p·S |
| Inode max file | B\[D + B/P + (B/P)² + (B/P)³\] |
| Deadlock-free resources | Σ(mᵢ−1) + 1 |
| Disk access time | seek + rotational latency + transfer |
| RM bound | n(2^(1/n) − 1) |
| fork(n) | 2ⁿ processes |

---

## 12. High-Yield Traps and Shortcuts

1. **Scheduling**: always draw the Gantt chart, mark arrivals, and handle idle CPU. RR tie-break between arriving and preempted process must follow the question's convention.
2. SJF is optimal for average waiting time only when all jobs are available together; SRTF is the preemptive version that's optimal in general.
3. FCFS has the convoy effect; priority scheduling needs aging; RR with infinite quantum = FCFS.
4. **Belady's anomaly** only in FIFO (and some others, never in stack algorithms). Optimal ≤ LRU ≤ FIFO in faults is typical but not guaranteed across FIFO vs LRU.
5. **Semaphore traps**: reversing `wait(empty)` and `wait(mutex)` in the producer causes deadlock. Initial values: mutex=1, empty=N, full=0. Check minimum/maximum value of a shared counter by interleaving load/store.
6. **Peterson's** works for two processes with a shared `turn` and `flag[]`; if the assignments are reordered, mutual exclusion fails. A lock built from plain read-then-write is broken.
7. Deadlock: cycle in RAG only *implies* deadlock for single-instance resources; safe state is not the same as no deadlock; unsafe may still run without deadlock.
8. **Banker's**: recompute Need = Max − Allocation, test in order and repeat passes; different orders may give different safe sequences; count the number of safe sequences if asked.
9. Page-table questions: compute the number of page-table entries from the **virtual** address space, not physical memory; inverted page table from physical memory.
10. **Multilevel paging**: each level's table must fit exactly in one page → bits per level = log₂(page size / PTE size); leftover bits go to the top level.
11. TLB: the formula changes with the number of page-table levels and if the TLB lookup time is given. Use `(levels + 1)·m` on a TLB miss.
12. Thrashing ⇒ CPU utilisation **drops** as the degree of multiprogramming increases beyond a limit.
13. Internal fragmentation: paging and fixed partitions; external: segmentation and variable partitions. Compaction needs dynamic relocation.
14. Hard links cannot span file systems; deleting one name of a hard-linked file doesn't remove data; symlinks can dangle.
15. Inode: direct blocks + indirect levels; convert sizes carefully (KB/MB/GB) and remember the pointer size; the largest file may also be limited by the disk address (block number) size.
16. `fork()` counting: draw the tree; shared variables are **not** shared between parent and child (separate address spaces); threads *do* share globals.
17. User-level threads: blocking syscall blocks the whole process; kernel threads: better concurrency; thread context switch is cheaper than process context switch.
18. Disk scheduling: check the current direction and whether the head goes to the physical end (SCAN) or the last request (LOOK); count total cylinders moved, not sectors.
19. Interrupt vs trap, privileged instruction list, and what goes in the PCB are frequent one-mark theory questions. A system call uses a trap; `fork` returns 0 in the child.
20. **Spinlock vs blocking**: spin is good when the wait is shorter than a context switch; useless on a single CPU.

---

## 13. Practice Plan

1. **PYQs topic-wise (GATE CSE 2010–2026)**: start with scheduling and synchronization (semaphores/fork), then deadlock/Banker's, then paging/TLB/virtual memory, then file system and disk numericals.
2. Hand-simulate: FCFS/SJF/SRTF/RR/priority Gantt charts, FIFO/LRU/OPT with 3–4 frames, Banker's safety check, disk scheduling sequences.
3. Memorise the formula sheet (section 11) and the classic semaphore solutions (section 6).
4. Keep an **error log** of trap + concept; revise only it in the last two weeks.
5. Practise unit conversions (KB/MB/GB, bits/bytes) separately; they cause most careless errors in paging and inode questions.
6. Take full-length mocks; OS often gives 7–10 marks, and most of it is formulaic once you practise the patterns above.
