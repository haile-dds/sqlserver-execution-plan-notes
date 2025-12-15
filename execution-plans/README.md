
---

# 2️⃣ `execution-plans/README.md` — Execution Plans Overview (EN)

# 🧠 Execution Plans & Query Optimizer

This folder focuses on **SQL Server Execution Plans**, how the **Query Optimizer makes decisions**,  
and how to read execution plans to troubleshoot performance issues in production systems.

> 🎯 Goal: Not just to see *what the plan does*, but to understand *why SQL Server chose it*.

---

## 📌 Contents

### 🔹 Execution Plan Fundamentals
- [Execution Plan – Properties Reference](execution-plan-properties.md)  
  A detailed explanation of **each property shown in SQL Server execution plans**
  (Estimated Cost, Rows, Memory Grant, Rebinds, and more).

---

### 🔹 Reading & Analysis (Roadmap)
- Execution Plan Reading Checklist *(coming soon)*
- Estimated vs Actual Rows *(coming soon)*
- Common Execution Plan Red Flags *(coming soon)*

---

### 🔹 Optimizer Internals (Roadmap)
- Cardinality Estimation *(coming soon)*
- Join Selection (Nested Loop / Hash / Merge) *(coming soon)*
- Memory Grants & Spills *(coming soon)*

---

## 🧩 How to Use These Notes

When analyzing a slow query:

1. Capture the **Actual Execution Plan**
2. Hover over operators and open **Properties (F4)**
3. Focus on:
   - Row estimates vs actual rows
   - Cost distribution
   - Memory grants and spills
4. Use these notes to decide:
   - Whether an index is needed
   - Whether the query should be rewritten
   - Whether statistics or data distribution is the root cause

---

## 🚨 Key Principles When Reading Execution Plans

- **Never trust cost values in isolation** — use them only for comparison
- **Row estimation errors matter more than operator cost**
- SORT, HASH, and KEY LOOKUP operators deserve immediate attention
- A “clean-looking” plan with bad estimates is still a bad plan

---

## 🔗 References

- Paul White – SQL Server Query Optimizer Internals
- Brent Ozar – How to Read Execution Plans
- Erik Darling – Execution Plans in the Real World
- Microsoft Docs – Showplan XML

---

⬅️ [Back to SQL Server Knowledge Base](../README.md)
