# 186. Which Operator Is Used in a Query for Pattern Matching?

> **Goal:** find rows where a text column **matches a pattern** instead of an exact value.
> Example: students whose name starts with "a", or names with exactly 4 letters.

---

## Table of Contents

1. [Quick Answer](#1-quick-answer)
2. [The Two Wildcards](#2-the-two-wildcards)
3. [Sample Data](#3-sample-data)
4. [Examples and Their Outcome](#4-examples-and-their-outcome)
5. [NOT LIKE](#5-not-like)
6. [Matching a Literal % or _ (ESCAPE)](#6-matching-a-literal--or-_-escape)
7. [Case Sensitivity](#7-case-sensitivity)
8. [NULL Handling](#8-null-handling)
9. [Extra Wildcards and Alternatives (by Database)](#9-extra-wildcards-and-alternatives-by-database)
10. [Performance Notes](#10-performance-notes)
11. [Common Mistakes](#11-common-mistakes)
12. [Interview Questions & Answers](#12-interview-questions--answers)
13. [Practice Exercises](#13-practice-exercises)
14. [Cheat Sheet](#14-cheat-sheet)
15. [Setup Script (Run It Yourself)](#15-setup-script-run-it-yourself)

---

## 1. Quick Answer

The **LIKE** operator is used for pattern matching. It works with two wildcards:

| Wildcard | Meaning |
|---|---|
| `%` | Matches **zero or more** characters |
| `_` (underscore) | Matches **exactly one** character |

```sql
SELECT * FROM Student WHERE studentname LIKE 'a%';    -- starts with "a"
SELECT * FROM Student WHERE studentname LIKE 'ami_';  -- "ami" + exactly one character
```

> **Interview-style answer:** "LIKE is used for pattern matching. `%` matches zero or more characters and `_` matches exactly one character. Use NOT LIKE for the opposite, and ESCAPE when you need to search for a literal `%` or `_`."

---

## 2. The Two Wildcards

### 2.1 `%` — zero or more characters

```sql
'a%'     -- starts with a          (a, amit, anita ...)
'%a'     -- ends with a            (a, anita, priya ...)
'%a%'    -- contains a anywhere    (a, rahul, kamala ...)
'a%a'    -- starts AND ends with a (anita ...)
'%'      -- any value (but NOT NULL)
```

`%` can also match **nothing**: `'a%'` matches the single letter `a`.

### 2.2 `_` — exactly one character

```sql
'ami_'   -- ami + exactly 1 char       (amit, amir)
'_a%'    -- 2nd character is a         (rahul, kamala)
'__m%'   -- 3rd character is m         (sumit, kamala)
'____'   -- exactly 4 characters       (amit, amir, aman)
'a_%'    -- starts with a, at least 2 characters
```

`_` must match **one** character. It never matches zero characters, so `'ami_'` does **not** match `ami`.

### 2.3 Combining them

| Pattern | Meaning |
|---|---|
| `'a%'` | starts with a |
| `'%a'` | ends with a |
| `'%a%'` | contains a |
| `'_a%'` | a is the 2nd character |
| `'a_%'` | starts with a, at least 2 characters |
| `'a__'` | starts with a, exactly 3 characters |
| `'_____%'` | at least 5 characters |
| `'%i_'` | i is the second-to-last character |

---

## 3. Sample Data

**Student**

| id | studentname |
|----|-------------|
| 1  | amit        |
| 2  | amir        |
| 3  | anita       |
| 4  | aman        |
| 5  | rahul       |
| 6  | sumit       |
| 7  | kamala      |
| 8  | priya       |

**Product** (used for the ESCAPE examples)

| ProductID | ProductCode |
|-----------|-------------|
| 1         | EMP_01      |
| 2         | EMP01       |
| 3         | EMPX01      |
| 4         | DISC_50%    |

---

## 4. Examples and Their Outcome

### 4.1 Starts with "a"
```sql
SELECT * FROM Student WHERE studentname LIKE 'a%';
```
| id | studentname |
|----|-------------|
| 1  | amit        |
| 2  | amir        |
| 3  | anita       |
| 4  | aman        |

### 4.2 "ami" followed by exactly one character
```sql
SELECT * FROM Student WHERE studentname LIKE 'ami_';
```
| id | studentname |
|----|-------------|
| 1  | amit        |
| 2  | amir        |

### 4.3 Ends with "a"
```sql
SELECT * FROM Student WHERE studentname LIKE '%a';
```
Result: anita, kamala, priya

### 4.4 Contains "am" anywhere
```sql
SELECT * FROM Student WHERE studentname LIKE '%am%';
```
Result: amit, amir, aman, kamala

### 4.5 Second letter is "a"
```sql
SELECT * FROM Student WHERE studentname LIKE '_a%';
```
Result: rahul, kamala

### 4.6 Exactly 4 characters long (four underscores)
```sql
SELECT * FROM Student WHERE studentname LIKE '____';
```
Result: amit, amir, aman

### 4.7 Ends with "it"
```sql
SELECT * FROM Student WHERE studentname LIKE '%it';
```
Result: amit, sumit

### 4.8 Starts and ends with "a"
```sql
SELECT * FROM Student WHERE studentname LIKE 'a%a';
```
Result: anita

### 4.9 Third letter is "m"
```sql
SELECT * FROM Student WHERE studentname LIKE '__m%';
```
Result: sumit, kamala

### 4.10 Several patterns at once (OR)
```sql
SELECT * FROM Student
WHERE studentname LIKE 'r%'
   OR studentname LIKE 'p%';
```
Result: rahul, priya

### Outcome of all examples on the sample data

| Pattern | Rows | Result |
|---|---|---|
| `'a%'` | 4 | amit, amir, anita, aman |
| `'ami_'` | 2 | amit, amir |
| `'%a'` | 3 | anita, kamala, priya |
| `'%am%'` | 4 | amit, amir, aman, kamala |
| `'_a%'` | 2 | rahul, kamala |
| `'____'` | 3 | amit, amir, aman |
| `'%it'` | 2 | amit, sumit |
| `'a%a'` | 1 | anita |
| `'__m%'` | 2 | sumit, kamala |
| `'%a%'` | 7 | everyone except sumit |
| `'%'` | 8 | all rows (non-NULL) |

---

## 5. NOT LIKE

`NOT LIKE` returns rows that do **not** match the pattern.

```sql
SELECT * FROM Student WHERE studentname NOT LIKE 'a%';
```
| id | studentname |
|----|-------------|
| 5  | rahul       |
| 6  | sumit       |
| 7  | kamala      |
| 8  | priya       |

```sql
-- Names that do not contain the letter "a"
SELECT * FROM Student WHERE studentname NOT LIKE '%a%';
```
Result: sumit

> ⚠️ `NOT LIKE` does **not** return NULL values either. See [section 8](#8-null-handling).

---

## 6. Matching a Literal % or _ (ESCAPE)

`%` and `_` are wildcards, so you can't search for them directly. You need to **escape** them.

### 6.1 The underscore trap
```sql
-- ❌ Meant: codes that start with "EMP_"
SELECT * FROM Product WHERE ProductCode LIKE 'EMP_%';
```
| ProductCode |
|-------------|
| EMP_01      |
| EMP01       |
| EMPX01      |

The `_` matched **any** character, so `EMP01` and `EMPX01` came back too.

### 6.2 Using the ESCAPE clause (works in every major database)
```sql
-- ✅ "!" is declared as the escape character, so "!_" means a literal underscore
SELECT * FROM Product WHERE ProductCode LIKE 'EMP!_%' ESCAPE '!';
```
| ProductCode |
|-------------|
| EMP_01      |

```sql
-- Codes that end with a literal %
SELECT * FROM Product WHERE ProductCode LIKE '%!%' ESCAPE '!';
```
| ProductCode |
|-------------|
| DISC_50%    |

You can choose any character as the escape character, as long as it does not appear in the data you're searching for. `!` is a safe, portable choice.

### 6.3 Database-specific shortcuts

| Database | Default escape character | Alternative |
|---|---|---|
| MySQL | `\` (written as `'EMP\\_%'` inside a string) | `ESCAPE '!'` |
| PostgreSQL | `\` (`'EMP\_%'`) | `ESCAPE '!'` |
| SQL Server | none | Brackets: `'EMP[_]%'`, `'%[%]'` |
| Oracle | none | `ESCAPE '!'` |
| SQLite | none | `ESCAPE '!'` |

```sql
-- SQL Server only: brackets make the wildcard literal
SELECT * FROM Product WHERE ProductCode LIKE 'EMP[_]%';
```

### 6.4 Searching for a single quote
A single quote is not a wildcard, but it ends the string. Double it:
```sql
SELECT * FROM Student WHERE studentname LIKE '%''%';   -- e.g. o'brien
```

---

## 7. Case Sensitivity

Whether `'A%'` matches `amit` depends on the database (and on the column's collation).

| Database | LIKE is case-sensitive by default? | How to change it |
|---|---|---|
| MySQL | No (default collations are case-insensitive) | `LIKE BINARY 'A%'` or `COLLATE utf8mb4_bin` for case-sensitive |
| SQL Server | No (default collation is case-insensitive) | `COLLATE Latin1_General_CS_AS` for case-sensitive |
| PostgreSQL | **Yes** | `ILIKE` for case-insensitive |
| Oracle | **Yes** | `UPPER(col) LIKE 'A%'` or `REGEXP_LIKE(col, '^a', 'i')` |
| SQLite | No (for ASCII letters) | `PRAGMA case_sensitive_like = ON;` or use `GLOB` |

```sql
-- PostgreSQL: case-insensitive match
SELECT * FROM Student WHERE studentname ILIKE 'A%';

-- Portable way that works everywhere
SELECT * FROM Student WHERE LOWER(studentname) LIKE 'a%';
```

> Wrapping the column in `LOWER()` / `UPPER()` works in every database, but it usually stops the database from using a normal index on that column.

---

## 8. NULL Handling

`NULL LIKE 'anything'` is **unknown**, not true or false, so the row is excluded. This applies to both `LIKE` and `NOT LIKE`.

Add a student with no name:
```sql
INSERT INTO Student (id, studentname) VALUES (9, NULL);
```

| Query | Includes id 9? |
|---|---|
| `WHERE studentname LIKE '%'` | ❌ No |
| `WHERE studentname LIKE 'a%'` | ❌ No |
| `WHERE studentname NOT LIKE 'a%'` | ❌ No |
| `WHERE studentname IS NULL` | ✅ Yes |

So `LIKE '%'` means "any **non-NULL** value", not "every row".

To include NULLs explicitly:
```sql
SELECT * FROM Student
WHERE studentname NOT LIKE 'a%'
   OR studentname IS NULL;
```

---

## 9. Extra Wildcards and Alternatives (by Database)

### 9.1 SQL Server bracket wildcards
SQL Server's LIKE supports character sets, which standard SQL does not.

| Pattern | Meaning | Result on sample data |
|---|---|---|
| `'[ak]%'` | starts with a **or** k | amit, amir, anita, aman, kamala |
| `'[a-m]%'` | starts with a letter from a to m | amit, amir, anita, aman, kamala |
| `'[^a]%'` | does **not** start with a | rahul, sumit, kamala, priya |
| `'[aeiou]%'` | starts with a vowel | amit, amir, anita, aman |

```sql
-- SQL Server only
SELECT * FROM Student WHERE studentname LIKE '[aeiou]%';
```

In other databases, use `OR` with several LIKEs, or a regular expression.

### 9.2 Regular expressions (more powerful than LIKE)

| Database | Syntax | Example: starts with a vowel |
|---|---|---|
| MySQL | `REGEXP` / `RLIKE` | `WHERE studentname REGEXP '^[aeiou]'` |
| PostgreSQL | `~` (case-sensitive), `~*` (case-insensitive) | `WHERE studentname ~ '^[aeiou]'` |
| Oracle | `REGEXP_LIKE` | `WHERE REGEXP_LIKE(studentname, '^[aeiou]')` |
| SQL Server 2025+ | `REGEXP_LIKE` | `WHERE REGEXP_LIKE(studentname, '^[aeiou]')` |
| SQLite | `REGEXP` (only if an extension provides it) | — |

### 9.3 Other variants
- **PostgreSQL `SIMILAR TO`**: a mix of LIKE and regex. `WHERE studentname SIMILAR TO '(a|k)%'`.
- **SQLite `GLOB`**: uses `*` and `?` instead of `%` and `_`, and is case-sensitive. `WHERE studentname GLOB 'a*'`.
- **MS Access**: uses `*` and `?` in LIKE (`LIKE 'a*'`), not `%` and `_`.

---

## 10. Performance Notes

- **Prefix patterns can use an index.** `LIKE 'a%'` works like a range search ("from a to just before b"), so an index on `studentname` helps.
- **Leading wildcards usually can't.** `LIKE '%a'` and `LIKE '%am%'` must check every row (full scan), because the index is sorted from the first character.
- **Functions on the column hurt indexes.** `LOWER(studentname) LIKE 'a%'` usually can't use a plain index. Options: a case-insensitive collation, a function-based / expression index, or PostgreSQL `ILIKE` with a suitable index.
- **For "contains" searches on large tables**, consider:
  - Full-text search (`CONTAINS` in SQL Server, `MATCH ... AGAINST` in MySQL, `tsvector` in PostgreSQL).
  - PostgreSQL trigram indexes (`pg_trgm`), which can speed up `LIKE '%am%'`.
- **Check the plan** with `EXPLAIN` / "Show Execution Plan" when it matters.

| Pattern | Can use a normal index? |
|---|---|
| `'a%'` | ✅ Usually |
| `'ami_'` | ✅ Usually (prefix "ami") |
| `'%a'` | ❌ No |
| `'%am%'` | ❌ No |
| `'_a%'` | ❌ No (starts with a wildcard) |

---

## 11. Common Mistakes

### 11.1 Using `=` with wildcards
```sql
-- ❌ Looks for a name that is literally "a%"
SELECT * FROM Student WHERE studentname = 'a%';

-- ✅
SELECT * FROM Student WHERE studentname LIKE 'a%';
```
Wildcards only work with `LIKE`.

### 11.2 Using `*` and `?` instead of `%` and `_`
```sql
-- ❌ Returns nothing in SQL Server, MySQL, PostgreSQL, Oracle
SELECT * FROM Student WHERE studentname LIKE 'a*';
```
`*` and `?` belong to MS Access and SQLite `GLOB`. Standard SQL uses `%` and `_`.

### 11.3 Forgetting that `_` is a wildcard
```sql
-- ❌ Also matches EMP01 and EMPX01
SELECT * FROM Product WHERE ProductCode LIKE 'EMP_%';
```
Escape it: `LIKE 'EMP!_%' ESCAPE '!'` (or `'EMP[_]%'` in SQL Server). See [section 6](#6-matching-a-literal--or-_-escape).

### 11.4 Forgetting the `%`
```sql
-- ❌ Only matches a name that is exactly "a"
SELECT * FROM Student WHERE studentname LIKE 'a';
```
Without a wildcard, LIKE behaves like an exact comparison.

### 11.5 Expecting `_` to match zero characters
```sql
-- 'ami_' does NOT match "ami"
```
`_` is **exactly one** character. Use `%` for "zero or more".

### 11.6 Assuming the same case behavior everywhere
`LIKE 'A%'` finds `amit` in MySQL and SQL Server (default settings) but **not** in PostgreSQL or Oracle. See [section 7](#7-case-sensitivity).

### 11.7 Expecting `LIKE '%'` or `NOT LIKE` to return NULL rows
Neither returns NULLs. Add `OR column IS NULL` if you need them. See [section 8](#8-null-handling).

### 11.8 Mixing AND / OR without parentheses
```sql
-- ❌ AND runs first: (LIKE 'r%') OR (LIKE 'p%' AND id > 5)
SELECT * FROM Student
WHERE studentname LIKE 'r%' OR studentname LIKE 'p%' AND id > 5;

-- ✅
SELECT * FROM Student
WHERE (studentname LIKE 'r%' OR studentname LIKE 'p%') AND id > 5;
```

### 11.9 Trailing spaces in CHAR columns
Fixed-length `CHAR(10)` columns can store `'amit'` as `'amit      '` (padded with spaces). Then `LIKE '%t'` may fail because the last character is a space. Use `VARCHAR`, or trim: `RTRIM(studentname) LIKE '%t'`.

### 11.10 Using LIKE on numbers or dates
```sql
-- ⚠️ Relies on implicit conversion; behavior varies by database
SELECT * FROM Orders WHERE OrderDate LIKE '2026%';

-- ✅ Use ranges for dates
SELECT * FROM Orders
WHERE OrderDate >= '2026-01-01' AND OrderDate < '2027-01-01';
```
Ranges are clearer and can use indexes.

### 11.11 Leading wildcards on big tables
`LIKE '%something%'` scans the whole table. Fine for small tables, slow for millions of rows. Consider full-text search. See [section 10](#10-performance-notes).

---

## 12. Interview Questions & Answers

**Q1. Which operator is used for pattern matching in SQL?**
`LIKE`, with the wildcards `%` (zero or more characters) and `_` (exactly one character).

**Q2. What is the difference between `%` and `_`?**
`%` matches any number of characters, including zero. `_` matches exactly one character.

**Q3. Find names that start with "a".**
```sql
SELECT * FROM Student WHERE studentname LIKE 'a%';
```

**Q4. Find names whose second letter is "a".**
```sql
SELECT * FROM Student WHERE studentname LIKE '_a%';
```

**Q5. Find names with exactly 4 characters.**
```sql
SELECT * FROM Student WHERE studentname LIKE '____';
-- or: WHERE LENGTH(studentname) = 4   (LEN() in SQL Server)
```

**Q6. What is the difference between `=` and `LIKE`?**
`=` compares exact values. `LIKE` compares against a pattern with wildcards. Without wildcards, LIKE behaves almost like `=`.

**Q7. How do you search for a literal `%` or `_`?**
Use the `ESCAPE` clause: `LIKE '%!%' ESCAPE '!'`. In SQL Server you can also use brackets: `LIKE '%[%]'`.

**Q8. Is LIKE case-sensitive?**
It depends on the database. MySQL and SQL Server are case-insensitive by default; PostgreSQL and Oracle are case-sensitive. PostgreSQL has `ILIKE` for case-insensitive matching. A portable approach is `LOWER(col) LIKE 'a%'`.

**Q9. Does `LIKE '%'` return all rows?**
No. It returns every row where the column is **not NULL**.

**Q10. Which is faster: `LIKE 'a%'` or `LIKE '%a'`?**
`LIKE 'a%'` is usually faster because it can use an index on the column. A leading `%` forces a full scan.

**Q11. How do you find names starting with a vowel?**
```sql
-- SQL Server
WHERE studentname LIKE '[aeiou]%'

-- Any database
WHERE studentname LIKE 'a%' OR studentname LIKE 'e%' OR studentname LIKE 'i%'
   OR studentname LIKE 'o%' OR studentname LIKE 'u%'

-- MySQL
WHERE studentname REGEXP '^[aeiou]'
```

**Q12. What's the opposite of LIKE?**
`NOT LIKE`. It returns rows that don't match the pattern (NULLs are still excluded).

**Q13. What if you need more complex patterns than LIKE allows?**
Use regular expressions: `REGEXP` (MySQL), `~` (PostgreSQL), `REGEXP_LIKE` (Oracle, SQL Server 2025+).

**Q14. How do you search for a name containing a single quote, like o'brien?**
Double the quote: `LIKE '%''%'`.

---

## 13. Practice Exercises

Use the sample data from [section 3](#3-sample-data).

**Exercise 1.** Return students whose name starts with "r".

<details>
<summary>Answer</summary>

```sql
SELECT * FROM Student WHERE studentname LIKE 'r%';
```
Result: rahul
</details>

**Exercise 2.** Return students whose name has "i" as the second-to-last character.

<details>
<summary>Answer</summary>

```sql
SELECT * FROM Student WHERE studentname LIKE '%i_';
```
Result: amit, amir, sumit
</details>

**Exercise 3.** Return students whose name has **at least** 5 characters.

<details>
<summary>Answer</summary>

```sql
SELECT * FROM Student WHERE studentname LIKE '_____%';
```
Result: anita, rahul, sumit, kamala, priya
</details>

**Exercise 4.** Return students whose name does **not** contain the letter "a".

<details>
<summary>Answer</summary>

```sql
SELECT * FROM Student WHERE studentname NOT LIKE '%a%';
```
Result: sumit
</details>

**Exercise 5.** Return product codes that start with the literal text `EMP_`.

<details>
<summary>Answer</summary>

```sql
SELECT * FROM Product WHERE ProductCode LIKE 'EMP!_%' ESCAPE '!';
-- SQL Server alternative: LIKE 'EMP[_]%'
```
Result: EMP_01
</details>

**Exercise 6.** A row `(9, NULL)` is added to Student. How many rows does `SELECT * FROM Student WHERE studentname LIKE '%';` return, and why?

<details>
<summary>Answer</summary>

8 rows. `NULL LIKE '%'` is unknown, not true, so the NULL row is excluded.
</details>

**Exercise 7.** Why does this return no rows in PostgreSQL, but 4 rows in MySQL?
```sql
SELECT * FROM Student WHERE studentname LIKE 'A%';
```

<details>
<summary>Answer</summary>

PostgreSQL's LIKE is case-sensitive, and all names are lowercase. MySQL's default collation is case-insensitive, so `'A%'` matches amit, amir, anita, aman. In PostgreSQL use `ILIKE 'A%'`.
</details>

**Exercise 8.** Return students whose third letter is "m".

<details>
<summary>Answer</summary>

```sql
SELECT * FROM Student WHERE studentname LIKE '__m%';
```
Result: sumit, kamala
</details>

---

## 14. Cheat Sheet

```sql
-- Starts with
WHERE col LIKE 'a%'

-- Ends with
WHERE col LIKE '%a'

-- Contains
WHERE col LIKE '%a%'

-- Exactly one unknown character
WHERE col LIKE 'ami_'

-- Nth character (here: 2nd)
WHERE col LIKE '_a%'

-- Exact length (here: 4)
WHERE col LIKE '____'

-- Does not match
WHERE col NOT LIKE 'a%'

-- Literal _ or %
WHERE col LIKE 'EMP!_%' ESCAPE '!'
WHERE col LIKE '%!%'    ESCAPE '!'

-- Case-insensitive (portable)
WHERE LOWER(col) LIKE 'a%'

-- Case-insensitive (PostgreSQL)
WHERE col ILIKE 'a%'

-- Character sets (SQL Server only)
WHERE col LIKE '[aeiou]%'
WHERE col LIKE '[^a]%'
```

**Remember:**
- `%` = zero or more characters. `_` = exactly one character.
- Wildcards only work with `LIKE`, never with `=`.
- `LIKE` / `NOT LIKE` never return NULL rows.
- Case sensitivity depends on the database and collation.
- `'a%'` can use an index; `'%a'` and `'%a%'` usually can't.
- Escape `_` and `%` when you mean them literally.

---

## 15. Setup Script (Run It Yourself)

Works in SQLite, PostgreSQL, SQL Server and MySQL.

```sql
CREATE TABLE Student (
    id          INT PRIMARY KEY,
    studentname VARCHAR(50)
);

CREATE TABLE Product (
    ProductID   INT PRIMARY KEY,
    ProductCode VARCHAR(20)
);

INSERT INTO Student (id, studentname) VALUES
(1, 'amit'),
(2, 'amir'),
(3, 'anita'),
(4, 'aman'),
(5, 'rahul'),
(6, 'sumit'),
(7, 'kamala'),
(8, 'priya');

INSERT INTO Product (ProductID, ProductCode) VALUES
(1, 'EMP_01'),
(2, 'EMP01'),
(3, 'EMPX01'),
(4, 'DISC_50%');

-- Optional: NULL demo (section 8)
-- INSERT INTO Student (id, studentname) VALUES (9, NULL);
```
