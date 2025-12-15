# SQL Server Execution Plan – Properties Reference (DBA Level)

This document provides a **detailed explanation of SQL Server Execution Plan properties**,  
as displayed when hovering over an operator or opening the **Properties window (F4)** in SSMS.

> 🎯 **Goal**: Understand **why the Query Optimizer chose a plan**, not just whether the query ran fast or slow.

---

## 1. Cost & Estimation (Core of the Cost-Based Optimizer)

| Property | Description | When It Matters | When to Ignore |
|--------|-------------|----------------|----------------|
| **Estimated Operator Cost** | Estimated cost of this operator alone | Compare operators within the same plan | Never use as runtime |
| **Estimated Subtree Cost** | Total cost of this operator plus all child operators | Compare overall plans | Do not compare across servers |
| **Estimated I/O Cost** | Estimated logical I/O cost | Identify I/O-heavy operators | Cache warmth can distort reality |
| **Estimated CPU Cost** | Estimated CPU usage | Identify CPU-bound operations | Less useful for IO-bound plans |
| **Cost %** | Percentage of total plan cost | Quickly find bottlenecks | Not an absolute metric |

📌 **Golden rule**  
> The **subtree cost of the root operator represents the total estimated cost of the plan**.

---

## 2. Rows & Cardinality (Root Cause of Most Performance Issues)

| Property | Description | Why It Matters |
|--------|-------------|----------------|
| **Estimated Number of Rows** | Optimizer’s row estimate | Drives join choice, memory grants |
| **Actual Number of Rows** | Actual rows processed | Reveals estimation errors |
| **Estimated Rows Per Execution** | Rows per execution | Important for Nested Loops |
| **Actual Rows Per Execution** | Actual rows per execution | Detects loop explosion |

🚨 **Critical red flag**
- Estimated = 1
- Actual = 100,000


This often leads to:
- Incorrect join strategies
- Excessive memory grants or spills
- Poor TempDB usage
- Unstable performance

---

## 3. Memory & TempDB Behavior

| Property | Description | When It Matters |
|--------|-------------|----------------|
| **Memory Grant** | Memory requested by the query | Sorts, Hash Joins |
| **Granted Memory** | Memory actually granted | Memory pressure |
| **Used Memory** | Memory actually used | Over- or under-grant |
| **Spill Level** | Spill to TempDB (Level 1–3) | Severe performance degradation |
| **Warnings** | Spill / Missing Index | Always investigate |

⚠️ **Spills are among the most expensive performance problems in SQL Server**.

---

## 4. Join & Loop Mechanics

| Property | Description | When It Matters |
|--------|-------------|----------------|
| **Logical Operation** | Logical join type (INNER, LEFT, etc.) | Business correctness |
| **Physical Operation** | Join algorithm (Nested Loop / Hash / Merge) | Performance |
| **Rebinds** | Number of times the inner side is re-evaluated | Nested Loop scalability |
| **Rewinds** | Number of times the inner side is reused | Usually good |
| **Estimated Rebinds** | Optimizer’s expectation | Wrong values indicate bad estimates |

🚨 **Nested Loop + high Rebinds** = scalability risk.

---

## 5. Access Methods (Scan vs Seek)

| Property | Description | When It Matters |
|--------|-------------|----------------|
| **Index Name** | Index used by the operator | Index correctness |
| **Seek Predicate** | Predicate used to locate rows | Must exist for efficient seek |
| **Residual Predicate** | Filter applied after row access | Indicates incomplete index |
| **Ordered** | Whether output is ordered | Avoids SORT operators |
| **Scan Direction** | Forward / Backward | ORDER BY alignment |

📌 A large residual predicate usually indicates **missing or suboptimal index design**.

---

## 6. Parallelism

| Property | Description | When It Matters |
|--------|-------------|----------------|
| **Parallel** | Whether the operator runs in parallel | CPU usage |
| **Estimated DOP** | Estimated degree of parallelism | CPU pressure |
| **Actual DOP** | Actual degree of parallelism | Throttling / skew |
| **Wait Type** | CXPACKET / CXCONSUMER | Parallel inefficiency |

⚠️ Parallelism improves throughput but **can hurt latency if misused**.

---

## 7. Plan Cache & Compilation

| Property | Description | When It Matters |
|--------|-------------|----------------|
| **Cached Plan Size** | Memory used by the cached plan | Cache pressure |
| **Compile Time** | Time spent compiling the plan | Ad-hoc workloads |
| **Compile CPU** | CPU cost of compilation | Parameter sniffing |
| **Optimization Level** | Full vs Trivial optimization | Plan quality |

📌 Large plans + frequent recompiles = cache churn.

---

## 8. Sorting & Ordering

| Property | Description | When It Matters |
|--------|-------------|----------------|
| **Sort Warnings** | Indicates spills | Immediate attention |
| **Top-N Sort** | Optimized sort for TOP queries | Pagination |
| **Order By Columns** | Columns used for ordering | Index matching |
| **Distinct Sort** | Used for deduplication | High cost |

⚠️ **SORT is a blocking operator** and often the root of performance issues.

---

## 9. Advanced / Internal Properties (DBA Level)

| Property | Description | Notes |
|--------|-------------|-------|
| **NodeId** | Operator identifier | XML plan debugging |
| **Predicate** | Filter logic | Debug correctness |
| **Defined Values** | Output columns | Expression analysis |
| **Estimated Execution Mode** | Row / Batch mode | Columnstore relevance |

---

## 10. Execution Plan Reading Checklist (Practical)
- Compare estimated vs actual rows (MOST IMPORTANT)
- Identify operators with the highest cost %
- Look for SORT, HASH, and KEY LOOKUP operators
- Check memory grants and spills
- Review seek predicates vs residual predicates
- Watch for Nested Loops with high rebinds
- Analyze parallelism and skew

---

## Key Takeaway

> **Execution plan properties are diagnostic clues, not performance metrics.**  
> Always interpret them in context — especially row estimates, memory behavior, and join mechanics.

---

### Recommended Usage
- Use as a **checklist when debugging slow queries**
- Add to a **GitHub repository or internal Wiki**
- Use during **index reviews and query rewrites**

