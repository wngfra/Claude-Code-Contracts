# Thinking Frameworks — Novel Problems, Algorithms & Design

**Purpose:** Equip Claude with structured thinking for problems that don't fit templates. Use when the task involves unfamiliar domains, algorithmic challenges, or design decisions with non-obvious trade-offs.

---

## When to Use This Document

- The problem has no obvious off-the-shelf solution
- You need to design an algorithm, not just wire up libraries
- The task requires reasoning about trade-offs (time vs space, consistency vs availability, simplicity vs flexibility)
- You're working in an unfamiliar domain and need to decompose before building

---

## 1. Problem Decomposition Framework

### Step 1: Clarify the Invariants

Before writing any code, identify what must always be true:

```
Input invariants:   What can the input look like? What can't it?
Output invariants:  What must the output satisfy?
State invariants:   What must remain true throughout execution?
```

**Example — Rate limiter:**
```
Input:    request_id, timestamp (monotonically increasing)
Output:   allow/deny decision
State:    window counts never exceed configured limit
Invariant: For any sliding window of size W, count ≤ max_requests
```

### Step 2: Find the Core Subproblem

Most complex problems reduce to a simpler core. Ask:

1. **What's the simplest version of this problem?** (1 user, 1 item, no concurrency)
2. **What makes the real version harder?** (scale, concurrency, edge cases, distribution)
3. **Can I solve the simple version first, then layer complexity?**

```
Complex task: "Build a distributed task scheduler"
Core subproblem: "Execute tasks in dependency order"
    → This is topological sort
    → Layer 1: Single-machine topo sort
    → Layer 2: Add priority within dependency level
    → Layer 3: Distribute across workers
    → Layer 4: Handle worker failure and retry
```

### Step 3: Identify the Shape of the Solution

Before choosing data structures or algorithms, identify the computational shape:

| Shape | Signal | Approach |
|-------|--------|----------|
| **Transform** | Input → Output, no state | Pure function, pipeline |
| **Accumulate** | Running total, aggregate | Fold/reduce, streaming |
| **Search** | Find element satisfying condition | Index, hash, tree, binary search |
| **Graph** | Relationships, dependencies, reachability | BFS/DFS, topological sort, shortest path |
| **Optimize** | Best/min/max under constraints | DP, greedy, binary search on answer |
| **Partition** | Split into groups by property | Hash, sort + group, union-find |
| **Schedule** | Order tasks with constraints | Priority queue, topological sort, interval scheduling |
| **Synchronize** | Coordinate concurrent access | Locks, channels, CAS, event loops |

---

## 2. Algorithm Design Principles

### Choose Data Structures Before Algorithms

The right data structure makes the algorithm obvious. The wrong one makes it impossible.

| Need | Structure | Why |
|------|-----------|-----|
| Fast lookup by key | HashMap/Dict | O(1) average |
| Ordered iteration | BTreeMap/SortedDict | O(log n) ops, sorted traversal |
| Fast min/max | Heap/PriorityQueue | O(1) peek, O(log n) push/pop |
| Membership test | HashSet/BloomFilter | O(1) test |
| Range queries | Segment tree / BIT | O(log n) range ops |
| Prefix operations | Trie | O(k) for key length k |
| Disjoint groups | Union-Find | Near O(1) amortized |
| FIFO processing | Queue/Deque | O(1) push/pop |
| Undo/backtrack | Stack | O(1) push/pop |
| Relationships | Adjacency list/matrix | Graph algorithms |

### Complexity-First Thinking

Before implementing, write down the complexity contract:

```python
def find_kth_largest(nums: list[int], k: int) -> int:
    """Find the k-th largest element.

    Complexity:
        Time:  O(n) average via quickselect, O(n log n) worst case
        Space: O(1) — in-place partitioning

    Alternative rejected: Sort then index — O(n log n) always, simpler but slower.
    Alternative rejected: Max-heap k pops — O(n + k log n), better when k << n.
    """
```

### The Greedy Proof Habit

When using a greedy approach, state why it's correct:

```python
def min_meeting_rooms(intervals: list[tuple[int, int]]) -> int:
    """Minimum number of meeting rooms needed.

    Greedy correctness: Processing events chronologically, we must allocate
    a new room for every start that has no available room. A room becomes
    available at the earliest end time. Using a min-heap on end times gives
    us the optimal reuse — no room is freed earlier than necessary, so we
    never over-allocate.
    """
```

### Recognize Recurring Patterns

| Pattern | Recognizer | Core Technique |
|---------|-----------|----------------|
| "Find all subsets/combinations" | Exponential output | Backtracking with pruning |
| "Shortest/cheapest path" | Weighted graph | Dijkstra / Bellman-Ford |
| "Can this be done?" + small state space | Boolean + memo | DP / BFS on states |
| "Optimal split of sequence" | Linear structure + cost | DP on intervals or prefixes |
| "Process events in order" | Timeline with start/end | Sweep line |
| "Maintain running statistics" | Streaming data | Sliding window / monotonic deque |
| "Find pattern in string" | Text + pattern | KMP / Rabin-Karp / suffix structure |
| "Merge overlapping ranges" | Interval list | Sort + sweep |
| "Connected components" | Implicit graph | Union-Find / BFS |
| "Top-K / Kth element" | Partial ordering needed | Quickselect / Heap |

---

## 3. Handling Novel Domains

### The Exploration Protocol

When you encounter an unfamiliar domain:

1. **Define the vocabulary.** What are the domain's key nouns and verbs? Build a glossary as types.
   ```python
   # Domain: genomics pipeline
   @dataclass
   class Sequence:
       id: str
       bases: str  # ACGT string
       quality_scores: list[float]

   @dataclass
   class Alignment:
       query: Sequence
       reference: Sequence
       position: int
       cigar: str  # alignment operations
       score: float
   ```

2. **Model the constraints.** What rules does the domain impose?
   ```
   - Bases are only A, C, G, T (or N for unknown)
   - Quality scores are Phred-scaled: Q = -10 * log10(error_probability)
   - Alignments must be consistent with CIGAR string
   ```

3. **Find the domain's natural operations.** What transformations are fundamental?
   ```
   - align(query, reference) -> Alignment
   - call_variants(alignments) -> list[Variant]
   - filter_quality(variants, min_quality) -> list[Variant]
   ```

4. **Build from the domain up, not the framework down.** The domain model should exist independently of any web framework, database, or UI.

### The Analogy Bridge

When a domain is truly novel, find structural analogies to known problems:

```
"Build a dependency-aware deployment system"
  → Analogy: This is a DAG execution engine
  → Known solution: Topological sort + parallel execution of independent nodes
  → Adaptation: Nodes are deployment steps, edges are "must deploy before"

"Build a collaborative text editor"
  → Analogy: This is a distributed consensus problem on a sequence
  → Known solutions: OT (Operational Transform) or CRDT (Conflict-free Replicated Data Types)
  → Adaptation: Each keystroke is an operation, server resolves conflicts

"Build a cache invalidation system"
  → Analogy: This is a publish-subscribe + dependency tracking problem
  → Known solution: Observer pattern + dependency graph
  → Adaptation: Cache entries subscribe to their data sources, invalidation propagates through the graph
```

---

## 4. Design Decision Framework

### When Facing Trade-offs

Use this structure to make and document decisions:

```
Decision: [What are we choosing between?]
Options:
  A: [Description] — Pros: [...] Cons: [...]
  B: [Description] — Pros: [...] Cons: [...]
Chosen: [A or B]
Reason: [Why this option wins given our constraints]
Reversibility: [Easy/Hard to change later]
```

**Example:**
```python
# Decision: Caching strategy for user profiles
# Options:
#   A: Read-through cache (check cache first, fill on miss)
#      Pros: Simple, consistent reads. Cons: Cold start latency.
#   B: Write-through cache (update cache on every write)
#      Pros: Always warm. Cons: Write amplification, consistency window.
# Chosen: A (read-through)
# Reason: Read-heavy workload (100:1 read:write ratio), cold start acceptable.
# Reversibility: Easy — cache is behind an interface, swap strategy without caller changes.
```

### The Simplicity Test

Before adding complexity, pass these gates:

1. **Can I solve this without the abstraction?** If yes, skip the abstraction.
2. **Will this be called from more than one place?** If no, inline it.
3. **Does the abstraction make the code easier to read?** If no, it's premature.
4. **Can I explain this design in one sentence?** If no, it's too complex.

```python
# ❌ Premature abstraction
class UserFetchStrategyFactory:
    def create_strategy(self, source: str) -> UserFetchStrategy: ...

# ✅ Just fetch the user
def fetch_user(user_id: str, db: Database) -> User:
    return db.query(User).get(user_id)
```

---

## 5. Incremental Complexity Management

### Build in Layers, Verify Each Layer

```
Layer 0: Types and interfaces (compile/type-check)
Layer 1: Core logic with hardcoded data (unit test)
Layer 2: Wire to real data sources (integration test)
Layer 3: Add error handling and edge cases (edge case tests)
Layer 4: Add performance optimizations (benchmark)
Layer 5: Add observability (logging, metrics)
```

**Never skip layers.** If Layer 1 doesn't work, Layers 2-5 are wasted effort.

### The "Make It Work, Make It Right, Make It Fast" Protocol

```
1. Make it work:  Simplest correct implementation. Ugly is fine.
                  Test: Does it produce correct output for all inputs?

2. Make it right: Refactor for clarity. Apply code taste principles.
                  Test: Same tests pass. Code is readable and maintainable.

3. Make it fast:  Profile first. Optimize only measured bottlenecks.
                  Test: Same tests pass. Performance target met.
```

**Critical rule:** Never optimize before Step 1 is complete and tested. Premature optimization produces complex code that's both slow AND hard to fix.

---

## 6. Error Space Thinking

### Enumerate Failure Modes Before Coding

For any function, list what can go wrong before implementing the happy path:

```python
def transfer_funds(from_account: str, to_account: str, amount: Decimal) -> TransferResult:
    """
    Failure modes:
    1. from_account doesn't exist → NotFoundError
    2. to_account doesn't exist → NotFoundError
    3. amount <= 0 → ValidationError
    4. amount > from_account.balance → InsufficientFundsError
    5. from_account == to_account → ValidationError
    6. Database unavailable → DatabaseError (retry with backoff)
    7. Partial failure (debit succeeds, credit fails) → Use transaction
    8. Concurrent transfer depletes balance → Use SELECT FOR UPDATE
    """
```

This drives both the implementation and the test cases. Every failure mode becomes a test.

---

## 7. When to Use Advanced Techniques

### Concurrency

**Use when:** I/O-bound operations that can overlap (API calls, file reads, DB queries).
**Don't use when:** CPU-bound operations in Python (GIL), or when operations must be sequential.

```python
# Signal: Multiple independent I/O operations
# → Use asyncio.gather / concurrent.futures
async def fetch_all_users(user_ids: list[str]) -> list[User]:
    return await asyncio.gather(*[fetch_user(uid) for uid in user_ids])
```

### Caching

**Use when:** Same computation requested repeatedly, result doesn't change often.
**Don't use when:** Data changes frequently, cache invalidation is complex, memory is constrained.

### Streaming/Generators

**Use when:** Data doesn't fit in memory, or you only need partial results.
**Don't use when:** You need random access, or the dataset is small.

```python
# Signal: Processing large file line by line
# → Use generator, not list
def parse_log_entries(path: Path) -> Iterator[LogEntry]:
    with open(path) as f:
        for line in f:
            yield parse_line(line)
```

---

## Integration with Contract System

This document supplements the contract. Use it when:

- **Generate mode:** Designing the architecture for a new system
- **Refactor mode:** Choosing which patterns to refactor toward
- **Debug mode:** Reasoning about root causes in complex systems
- **Partial Rewrite mode:** Designing the replacement module's internals

The contract defines **what** quality looks like. This document helps you **think** your way to quality when the path isn't obvious.
