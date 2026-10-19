# SQL Server / T-SQL Interview Study

A personal study repo for preparing **SQL interviews** (with a focus on SQL Server / T-SQL).
It holds concept guides, worked exercises and a **mock interview question bank** you can use
to practice on your own before reading the answers.

---

## How to use this repo

1. Open [mock-interview-questions.md](mock-interview-questions.md).
2. Pick a difficulty level (🟢 Easy → 🟡 Medium → 🔴 Hard) or a topic.
3. Answer the question **out loud or on paper first**, as if you were in the interview.
   For query questions, write the SQL before checking anything.
4. Click the **Answer** link next to the question. It jumps to the explanation in `source-database/`.
5. Mark the checkbox when you can answer it confidently without looking.

> Tip: time yourself. A concept question should take 1–2 minutes to answer;
> a query exercise 5–10 minutes.

---

## Repository structure

```
sql-server-tsql-study/
├── README.md                       ← you are here
├── mock-interview-questions.md     ← practice questions (no answers, links to sources)
└── source-database/                ← the answer sources: concept guides + exercises
    ├── 186-pattern-matching-like.md
    ├── sql-180-common-records-two-tables.md
    ├── sql-employee-hierarchy-exercise.md
    ├── sql-server-query-optimization-guide.md
    ├── sql-subquery-types.md
    ├── sql_unpivoting_guide.md
    ├── tsql-duplicates-and-max-per-group.md
    ├── tsql-local-vs-global-variables.md
    └── tsql_cheatsheet_dark.pdf
```

---

## Topics covered

| Topic | Source file | Level |
|---|---|---|
| Pattern matching with `LIKE`, wildcards, `ESCAPE` | [186-pattern-matching-like.md](source-database/186-pattern-matching-like.md) | 🟢 Easy → 🟡 Medium |
| Common records from two tables (`INTERSECT`, `JOIN`, `IN`, `EXISTS`) | [sql-180-common-records-two-tables.md](source-database/sql-180-common-records-two-tables.md) | 🟢 Easy → 🟡 Medium |
| Local vs global variables (`@var`, `@@ROWCOUNT`, `SCOPE_IDENTITY()`) | [tsql-local-vs-global-variables.md](source-database/tsql-local-vs-global-variables.md) | 🟢 Easy → 🟡 Medium |
| Subquery types (correlated / non-correlated, `ANY` / `ALL`) | [sql-subquery-types.md](source-database/sql-subquery-types.md) | 🟡 Medium |
| Unpivoting columns into rows, `OR` vs `AND` logic | [sql_unpivoting_guide.md](source-database/sql_unpivoting_guide.md) | 🟡 Medium |
| Duplicates and max value per group (`GROUP BY`, window functions) | [tsql-duplicates-and-max-per-group.md](source-database/tsql-duplicates-and-max-per-group.md) | 🟡 Medium → 🔴 Hard |
| Employee hierarchy with recursive CTEs | [sql-employee-hierarchy-exercise.md](source-database/sql-employee-hierarchy-exercise.md) | 🟡 Medium → 🔴 Hard |
| Query optimization (execution plans, indexes, statistics, blocking) | [sql-server-query-optimization-guide.md](source-database/sql-server-query-optimization-guide.md) | 🔴 Hard |
| T-SQL quick reference | [tsql_cheatsheet_dark.pdf](source-database/tsql_cheatsheet_dark.pdf) | Reference |

---

## Difficulty legend

| Level | Meaning |
|---|---|
| 🟢 Easy | Definitions and basic syntax. Expected from any SQL developer. |
| 🟡 Medium | Writing real queries, explaining trade-offs, `NULL` traps, subqueries, CTEs. |
| 🔴 Hard | Window functions, recursion, performance tuning, locking, edge cases. |

---

## Running the examples

Most source files include a **setup script** with `CREATE TABLE` and `INSERT` statements.
You can run them on:

- SQL Server (Developer or Express edition) with SSMS or Azure Data Studio
- An online sandbox such as [sqlfiddle](https://sqlfiddle.com/) or [db<>fiddle](https://dbfiddle.uk/)

---

## Adding new material

1. Add the new guide to `source-database/` (one topic per file, with an
   *Interview Questions* or *Practice Exercises* section).
2. Add its questions to [mock-interview-questions.md](mock-interview-questions.md)
   **without the answer**, and link the **Answer** to the matching section of the new file.
3. Add a row to the *Topics covered* table above.
