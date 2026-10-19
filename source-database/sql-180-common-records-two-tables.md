# 180. How to Fetch Common Records from Two Tables

> **Goal:** return only the records that exist in **both** tables.
> Example: students (`Student` table) who have taken at least one exam (`Exam` table).

---

## Table of Contents

1. [Quick Answer](#1-quick-answer)
2. [The Four Solutions](#2-the-four-solutions)
3. [Sample Data](#3-sample-data)
4. [JOIN without DISTINCT vs INTERSECT — Seeing the Outcome](#4-join-without-distinct-vs-intersect--seeing-the-outcome)
5. [Key Differences Between the Methods](#5-key-differences-between-the-methods)
6. [NULL Handling in Detail](#6-null-handling-in-detail)
7. [Database Support](#7-database-support)
8. [Performance Notes](#8-performance-notes)
9. [Common Mistakes](#9-common-mistakes)
10. [Interview Questions & Answers](#10-interview-questions--answers)
11. [Practice Exercises](#11-practice-exercises)
12. [Cheat Sheet](#12-cheat-sheet)
13. [Setup Script (Run It Yourself)](#13-setup-script-run-it-yourself)

---

## 1. Quick Answer

Common records can be fetched with **INTERSECT**:

```sql
SELECT StudentID FROM Student
INTERSECT
SELECT StudentID FROM Exam;
```

Yes, a **JOIN** is another solution, and so are **IN** and **EXISTS**:

> **Interview-style answer:** "Use INTERSECT, or an INNER JOIN with DISTINCT, or IN / EXISTS. With a JOIN, remember that it can return duplicates."

---

## 2. The Four Solutions

### 2.1 INTERSECT

```sql
SELECT StudentID FROM Student
INTERSECT
SELECT StudentID FROM Exam;
```

- Compares the **result sets** of two queries.
- Returns values that appear in both.
- Removes duplicates automatically.

### 2.2 INNER JOIN

```sql
SELECT DISTINCT s.StudentID
FROM Student s
INNER JOIN Exam e ON s.StudentID = e.StudentID;
```

- Matches rows by a condition.
- Produces **one row per matching pair**, so `DISTINCT` is needed to remove duplicates.
- Lets you select columns from **both** tables.

### 2.3 IN (subquery)

```sql
SELECT StudentID
FROM Student
WHERE StudentID IN (SELECT StudentID FROM Exam);
```

- Keeps each `Student` row whose ID appears in the subquery result.
- Each Student row is returned **once**, no DISTINCT needed (as long as `Student` itself has no duplicate IDs).

### 2.4 EXISTS

```sql
SELECT s.StudentID
FROM Student s
WHERE EXISTS (
    SELECT 1
    FROM Exam e
    WHERE e.StudentID = s.StudentID
);
```

- A correlated subquery: for each student, "is there at least one exam?"
- Each Student row is returned **once**.
- Can stop searching at the first match, so it's often efficient on large tables.

---

## 3. Sample Data

**Student**

| StudentID | Name  |
|-----------|-------|
| 1         | Ana   |
| 2         | Luis  |
| 3         | Maria |

**Exam**

| ExamID | StudentID | Subject |
|--------|-----------|---------|
| 101    | 1         | Math    |
| 102    | 1         | Science |
| 103    | 1         | History |
| 104    | 2         | Math    |

- Ana (1) has **3** exams
- Luis (2) has **1** exam
- Maria (3) has **no** exams

Expected common records: **1 and 2**.

---

## 4. JOIN without DISTINCT vs INTERSECT — Seeing the Outcome

### INTERSECT

```sql
SELECT StudentID FROM Student
INTERSECT
SELECT StudentID FROM Exam;
```

| StudentID |
|-----------|
| 1         |
| 2         |

**2 rows.** Each common value appears once.

### JOIN without DISTINCT

```sql
SELECT s.StudentID
FROM Student s
INNER JOIN Exam e ON s.StudentID = e.StudentID;
```

| StudentID |
|-----------|
| 1         |
| 1         |
| 1         |
| 2         |

**4 rows.** The join makes one row for each matching pair, so StudentID 1 matches 3 Exam rows and appears 3 times.

### JOIN with DISTINCT

```sql
SELECT DISTINCT s.StudentID
FROM Student s
INNER JOIN Exam e ON s.StudentID = e.StudentID;
```

| StudentID |
|-----------|
| 1         |
| 2         |

**2 rows.** Same result as INTERSECT.

### Why?

- **INTERSECT** compares *sets of values* and removes duplicates automatically.
- **JOIN** *multiplies rows*: each Student row is repeated once for every Exam row it matches.
- Use `DISTINCT` to get INTERSECT's result from a JOIN.

### Bonus: what the JOIN is really doing

Selecting more columns makes the "one row per matching pair" behavior obvious:

```sql
SELECT s.StudentID, s.Name, e.ExamID, e.Subject
FROM Student s
INNER JOIN Exam e ON s.StudentID = e.StudentID;
```

| StudentID | Name | ExamID | Subject |
|-----------|------|--------|---------|
| 1         | Ana  | 101    | Math    |
| 1         | Ana  | 102    | Science |
| 1         | Ana  | 103    | History |
| 2         | Luis | 104    | Math    |

### Outcome of all four methods on the sample data

| Method                  | Rows returned | Result        |
|-------------------------|---------------|---------------|
| INTERSECT               | 2             | 1, 2          |
| INNER JOIN (no DISTINCT)| 4             | 1, 1, 1, 2    |
| INNER JOIN + DISTINCT   | 2             | 1, 2          |
| IN                      | 2             | 1, 2          |
| EXISTS                  | 2             | 1, 2          |

---

## 5. Key Differences Between the Methods

| Aspect | INTERSECT | INNER JOIN | IN | EXISTS |
|---|---|---|---|---|
| Duplicates | Removed automatically | One row per match (need DISTINCT) | Each outer row once | Each outer row once |
| NULL = NULL treated as match? | **Yes** | No | No | No |
| Columns from both tables? | No (same column list on both sides) | **Yes** | Only outer table | Only outer table |
| Compares multiple columns? | Yes, the whole row must match | Yes, via `AND` in `ON` | Some DBs support `(a, b) IN (...)` | Yes, via `AND` in `WHERE` |
| Column count/types must match? | **Yes** | No | Subquery returns 1 column (usually) | No |
| Typical use | Pure set comparison | Need data from both tables | Simple "is in list" check | "Does a match exist?" on big tables |

---

## 6. NULL Handling in Detail

Set operators (`INTERSECT`, `UNION`, `EXCEPT`) treat two NULLs as **equal**.
Comparison operators (`=` in JOIN, IN, EXISTS) do **not**: `NULL = NULL` is *unknown*, not true.

Example with a nullable column:

**TableA**

| Val  |
|------|
| 1    |
| NULL |

**TableB**

| Val  |
|------|
| 1    |
| NULL |

```sql
-- INTERSECT
SELECT Val FROM TableA
INTERSECT
SELECT Val FROM TableB;
```

| Val  |
|------|
| NULL |
| 1    |

```sql
-- INNER JOIN
SELECT a.Val
FROM TableA a
INNER JOIN TableB b ON a.Val = b.Val;
```

| Val |
|-----|
| 1   |

- INTERSECT returns **2 rows** (NULL is matched).
- JOIN returns **1 row** (NULL is dropped).

> In practice this rarely matters for keys like `StudentID` (primary keys can't be NULL), but it matters a lot when comparing nullable columns such as `Email`, `Phone` or `MiddleName`.

If you need a JOIN to match NULLs:

```sql
-- Standard SQL / PostgreSQL / SQL Server 2022+
ON a.Val IS NOT DISTINCT FROM b.Val

-- MySQL
ON a.Val <=> b.Val

-- Any database
ON (a.Val = b.Val OR (a.Val IS NULL AND b.Val IS NULL))
```

---

## 7. Database Support

| Database | INTERSECT | Notes |
|---|---|---|
| SQL Server | ✅ | |
| Oracle | ✅ | Oracle also uses `MINUS` (equivalent of `EXCEPT`) |
| PostgreSQL | ✅ | Also supports `INTERSECT ALL` |
| SQLite | ✅ | |
| MySQL | ✅ only from **8.0.31** | On older MySQL / MariaDB versions, use JOIN, IN or EXISTS |

**INTERSECT ALL** keeps duplicates (the minimum count from each side). Not every database supports it, so check yours before using it.

---

## 8. Performance Notes

- **EXISTS** can stop at the first matching row, which makes it efficient for "does a match exist" checks on large tables.
- **JOIN + DISTINCT** builds every matching pair first, then removes duplicates. On large one-to-many relationships, that can be expensive.
- **INTERSECT** needs a sort or hash to compare sets and remove duplicates.
- **Modern optimizers** often produce similar execution plans for IN, EXISTS and JOIN. When it matters, compare the actual execution plan (`EXPLAIN` / "Show Execution Plan").
- An **index on the join column** (`Exam.StudentID`) helps every method.

---

## 9. Common Mistakes

### 9.1 Forgetting DISTINCT on the JOIN
```sql
-- ❌ Returns 1, 1, 1, 2
SELECT s.StudentID
FROM Student s
JOIN Exam e ON s.StudentID = e.StudentID;
```
In a one-to-many relationship, the JOIN repeats the "one" side for every match. Use `DISTINCT`, or use `EXISTS` / `IN` instead.

### 9.2 Using DISTINCT to hide a bad JOIN
If you see unexpected duplicates, check first whether the join condition is correct or incomplete (for example, missing part of a composite key). DISTINCT can hide a real bug.

### 9.3 Using LEFT JOIN instead of INNER JOIN
```sql
-- ❌ Returns ALL students, including Maria (3) who has no exams
SELECT DISTINCT s.StudentID
FROM Student s
LEFT JOIN Exam e ON s.StudentID = e.StudentID;
```
A LEFT JOIN keeps unmatched rows. For **common** records you need an **INNER** JOIN.

### 9.4 Expecting INTERSECT to return columns from both tables
```sql
-- ❌ Error or wrong logic: column lists don't match
SELECT StudentID, Name FROM Student
INTERSECT
SELECT StudentID FROM Exam;
```
Both sides of INTERSECT must have the **same number of columns** with **compatible data types**. If you need `Name` from Student and `Subject` from Exam, use a JOIN.

### 9.5 INTERSECT on multiple columns when you only meant one
```sql
SELECT StudentID, Name FROM Student
INTERSECT
SELECT StudentID, Name FROM Student_Archive;
```
This only matches rows where **all** columns are equal. If the ID matches but the name has a different spelling, the row is excluded.

### 9.6 Using INTERSECT on an old MySQL version
INTERSECT doesn't exist before MySQL 8.0.31. You'll get a syntax error. Use JOIN, IN or EXISTS.

### 9.7 Ignoring NULL behavior
INTERSECT matches NULLs, while JOIN / IN / EXISTS don't. Two queries that "look equivalent" can return different results on nullable columns. See [section 6](#6-null-handling-in-detail).

### 9.8 Putting ORDER BY in the wrong place
```sql
-- ❌ Error in most databases
SELECT StudentID FROM Student ORDER BY StudentID
INTERSECT
SELECT StudentID FROM Exam;

-- ✅ ORDER BY goes once, at the very end
SELECT StudentID FROM Student
INTERSECT
SELECT StudentID FROM Exam
ORDER BY StudentID;
```

### 9.9 Confusing IN with NOT IN (related trap)
`IN` works fine for finding common records. But its opposite, `NOT IN`, returns **no rows at all** if the subquery contains a NULL:

```sql
-- ⚠️ If any Exam.StudentID is NULL, this returns nothing
SELECT StudentID FROM Student
WHERE StudentID NOT IN (SELECT StudentID FROM Exam);
```
For "records NOT in the other table", prefer `NOT EXISTS` or `EXCEPT`.

---

## 10. Interview Questions & Answers

**Q1. How do you fetch common records from two tables?**
Use `INTERSECT`, an `INNER JOIN` (with `DISTINCT`), `IN`, or `EXISTS`.

**Q2. Is a JOIN the same as INTERSECT?**
Not exactly. Both find matches, but:
- JOIN can return duplicates (one row per matching pair); INTERSECT returns distinct rows.
- INTERSECT treats NULLs as equal; JOIN doesn't.
- JOIN can return columns from both tables; INTERSECT can't.

**Q3. Why does my JOIN return more rows than INTERSECT?**
Because of a one-to-many relationship. A student with 3 exams matches 3 Exam rows, so the JOIN returns that student 3 times.

**Q4. How do you make a JOIN return the same result as INTERSECT?**
Add `DISTINCT` to the SELECT. (The only remaining difference is NULL handling.)

**Q5. Which is fastest?**
It depends on the database and data. EXISTS is often efficient because it stops at the first match. Modern optimizers often generate similar plans; check the execution plan.

**Q6. What are the rules for INTERSECT?**
- Same number of columns in both queries.
- Compatible data types, column by column.
- Column names come from the first query.
- `ORDER BY` only once, at the end.

**Q7. What's the opposite of INTERSECT?**
`EXCEPT` (SQL Server, PostgreSQL, SQLite, MySQL 8.0.31+) or `MINUS` (Oracle). It returns rows in the first query that are not in the second.

```sql
-- Students with no exams
SELECT StudentID FROM Student
EXCEPT
SELECT StudentID FROM Exam;
-- Result: 3
```

**Q8. Does INTERSECT remove duplicates?**
Yes. Use `INTERSECT ALL` (where supported) to keep them.

**Q9. Your database doesn't support INTERSECT. What do you do?**
Use `INNER JOIN` with `DISTINCT`, or `IN`, or `EXISTS`.

**Q10. You need the student's name AND the exam subject. Which method?**
INNER JOIN, because it's the only one that returns columns from both tables.

---

## 11. Practice Exercises

Use the sample data from [section 3](#3-sample-data).

**Exercise 1.** Return the **names** of students who have taken at least one exam, each name once.

<details>
<summary>Answer</summary>

```sql
SELECT s.Name
FROM Student s
WHERE EXISTS (SELECT 1 FROM Exam e WHERE e.StudentID = s.StudentID);
```
Result: Ana, Luis
</details>

**Exercise 2.** How many rows does this return, and why?
```sql
SELECT s.Name
FROM Student s
JOIN Exam e ON s.StudentID = e.StudentID;
```

<details>
<summary>Answer</summary>

4 rows: Ana, Ana, Ana, Luis. Ana matches 3 exams.
</details>

**Exercise 3.** Return each student who took an exam, with the **number of exams** they took.

<details>
<summary>Answer</summary>

```sql
SELECT s.StudentID, s.Name, COUNT(*) AS ExamCount
FROM Student s
JOIN Exam e ON s.StudentID = e.StudentID
GROUP BY s.StudentID, s.Name;
```
| StudentID | Name | ExamCount |
|---|---|---|
| 1 | Ana | 3 |
| 2 | Luis | 1 |
</details>

**Exercise 4.** Return students who have **not** taken any exam. Write it two ways.

<details>
<summary>Answer</summary>

```sql
-- EXCEPT
SELECT StudentID FROM Student
EXCEPT
SELECT StudentID FROM Exam;

-- NOT EXISTS
SELECT s.StudentID
FROM Student s
WHERE NOT EXISTS (SELECT 1 FROM Exam e WHERE e.StudentID = s.StudentID);
```
Result: 3 (Maria)
</details>

**Exercise 5.** Why does this query fail?
```sql
SELECT StudentID, Name FROM Student
INTERSECT
SELECT StudentID FROM Exam;
```

<details>
<summary>Answer</summary>

The two queries have a different number of columns (2 vs 1). INTERSECT requires the same number of columns with compatible types.
</details>

---

## 12. Cheat Sheet

```sql
-- Common values only, no duplicates
SELECT col FROM A INTERSECT SELECT col FROM B;

-- Need columns from both tables
SELECT DISTINCT a.col, b.other
FROM A a JOIN B b ON a.col = b.col;

-- Simple membership check
SELECT col FROM A WHERE col IN (SELECT col FROM B);

-- Efficient existence check
SELECT a.col FROM A a
WHERE EXISTS (SELECT 1 FROM B b WHERE b.col = a.col);

-- Opposite: in A but not in B
SELECT col FROM A EXCEPT SELECT col FROM B;   -- MINUS in Oracle
```

**Remember:**
- JOIN = one row per matching pair → watch for duplicates.
- INTERSECT = distinct set of common values; NULL matches NULL.
- IN / EXISTS = each outer row at most once.
- For "common records", use INNER JOIN, never LEFT JOIN.

---

## 13. Setup Script (Run It Yourself)

Works in SQLite, PostgreSQL, SQL Server and MySQL 8.0.31+.

```sql
CREATE TABLE Student (
    StudentID INT PRIMARY KEY,
    Name      VARCHAR(50)
);

CREATE TABLE Exam (
    ExamID    INT PRIMARY KEY,
    StudentID INT,
    Subject   VARCHAR(50)
);

INSERT INTO Student (StudentID, Name) VALUES
(1, 'Ana'),
(2, 'Luis'),
(3, 'Maria');

INSERT INTO Exam (ExamID, StudentID, Subject) VALUES
(101, 1, 'Math'),
(102, 1, 'Science'),
(103, 1, 'History'),
(104, 2, 'Math');

-- NULL demo tables
CREATE TABLE TableA (Val INT);
CREATE TABLE TableB (Val INT);
INSERT INTO TableA (Val) VALUES (1), (NULL);
INSERT INTO TableB (Val) VALUES (1), (NULL);
```
