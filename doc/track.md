# Practice Tracker — All Exercises

Sources: `mock-interview-practical-scenarios.md` (P1.1–P10.3) and `claude/180-common-records-two-tables.md` (section 11).

**Status:** ⬜ Pending · 🟨 In progress · ✅ Done
**Level:** 🟢 Easy · 🟡 Medium · 🔴 Hard
**Time:** estimated / actual minutes. **Date:** the day you finished it.

> Update a row when you finish: change ⬜ to ✅, fill in the actual time and the date.

## Progress by section

| Section | Exercises | Done | Est. time |
|---|---|---|---|
| 1. Duplicates and Max Value per Group | 5 | 0 / 5 | 85 min |
| 2. Recursive Hierarchy | 6 | 0 / 6 | 80 min |
| 3. Unpivoting Columns into Rows | 4 | 3 / 4 | 65 min |
| 4. Subqueries on Real Data | 8 | 4 / 8 | 110 min |
| 5. Common Records Between Tables | 4 | 0 / 4 | 45 min |
| 6. Window Functions and Gaps & Islands | 4 | 0 / 4 | 75 min |
| 7. Data Integrity and Constraints | 4 | 0 / 4 | 50 min |
| 8. Transactions and Concurrency | 5 | 0 / 5 | 100 min |
| 9. Performance Tuning | 4 | 0 / 4 | 70 min |
| 10. Time Zones, Bulk Loads and SQL Code Review | 3 | 0 / 3 | 40 min |
| 180. Common Records from Two Tables (section 11) | 5 | 0 / 5 | 27 min |
| **Total** | **52** | **7 / 52** | **747 min (~12.5 h)** |

## 1. Duplicates and Max Value per Group

Source: `mock-interview-practical-scenarios.md`

| Status | # | Exercise | Topic | Level | Time (est / actual) | Date | Note |
|---|---|---|---|---|---|---|---|
| ⬜ | P1.1 | Sales (Customers): repeat customers with count, max amount and its date | GROUP BY + correlated subquery / ROW_NUMBER | 🟡 Medium | 15 min / | | Solve twice: Approach A and B |
| ⬜ | P1.2 | Employee Logins: first/last login per repeat employee | Max/min per group, ordering by date + time | 🟡 Medium | 15 min / | | MIN/MAX(LoginTime) is wrong — order by date, then time |
| ⬜ | P1.3 | Product Orders: max quantity and its order date | Max per group | 🟡 Medium | 15 min / | | Solve twice: Approach A and B |
| ⬜ | P1.4 | Website Page Views | Window functions with ties | 🔴 Hard | 20 min / | | Watch for ties (RANK vs ROW_NUMBER) |
| ⬜ | P1.5 | Package Deliveries | Window functions with ties | 🔴 Hard | 20 min / | | Watch for ties (RANK vs ROW_NUMBER) |

## 2. Recursive Hierarchy

Source: `mock-interview-practical-scenarios.md`

| Status | # | Exercise | Topic | Level | Time (est / actual) | Date | Note |
|---|---|---|---|---|---|---|---|
| ⬜ | P2.1 | Hierarchy with level (CEO = 0) | Recursive CTE | 🟡 Medium | 15 min / | | Anchor = root, recursive member adds Level + 1 |
| ⬜ | P2.2 | Hierarchy path | Recursive CTE + string path | 🔴 Hard | 15 min / | | Cast path to VARCHAR(MAX) in anchor |
| ⬜ | P2.3 | All subordinates of one manager | Recursive CTE from a given node | 🟡 Medium | 15 min / | | Don't include the manager himself |
| ⬜ | P2.4 | Direct and total reports | Recursive CTE + aggregation | 🔴 Hard | 20 min / | | Direct = 1 level; total = all levels |
| ⬜ | P2.5 | Multiple root nodes | Recursive CTE anchors | 🟢 Easy | 5 min / | | Anchor on ManagerID IS NULL handles many roots |
| ⬜ | P2.6 | Orphan employees | LEFT JOIN / NOT EXISTS | 🟡 Medium | 10 min / | | Manager ID that doesn't exist |

## 3. Unpivoting Columns into Rows

Source: `mock-interview-practical-scenarios.md`

| Status | # | Exercise | Topic | Level | Time (est / actual) | Date | Note |
|---|---|---|---|---|---|---|---|
| ✅ | P3.1 | Address Lines | UNPIVOT / CROSS APPLY VALUES | 🟡 Medium | 20 min / 6 min | 2026-10-26 | Solved with UNION ALL ×4 — correct. Next: P3.3 (CROSS APPLY VALUES). See [Solutions](#p31--address-lines) |
| 🟨 | P3.2 | Contact Phone Numbers | UNPIVOT / CROSS APPLY VALUES | 🟡 Medium | 20 min / 13 min | | Attempt 2026-10-26: filters right, but `Email` column missing. See [Solutions](#p32--contact-phone-numbers) |
| ✅ | P3.3 | Phone Numbers with CROSS APPLY VALUES | CROSS APPLY VALUES, custom sort order | 🔴 Hard | 10 min / — | 2026-10-26 | Solved with AI assistance, not independently (1 min, not counted against the target). Redo from scratch later without help. See [Solutions](#p33--phone-numbers-with-cross-apply-values) |
| ✅ | P3.4 | Monthly Sales Import with CROSS APPLY VALUES | CROSS APPLY VALUES, safe text → DECIMAL, CASE status | 🔴 Hard | 15 min / 29 min | 2026-10-26 | Final query based on AI correction, not a clean unaided attempt (first try: mixed simple/searched CASE, cast the wrong column, no ORDER BY). Redo from scratch later without help. See [Solutions](#p34--monthly-sales-import-with-cross-apply-values) |

## 4. Subqueries on Real Data

Source: `mock-interview-practical-scenarios.md`

Attempt 2026-10-26: **1 h 20 min for the whole section** (assumed from "1.20"). No per-problem times were recorded. 4 of 8 passed; retry P4.3, P4.5, P4.7, P4.8.

| Status | # | Exercise | Topic | Level | Time (est / actual) | Date | Note |
|---|---|---|---|---|---|---|---|
| ✅ | P4.1 | Cheapest game(s) overall | Non-correlated subquery | 🟢 Easy | 10 min / | 2026-10-26 | Correct on the sample. `TOP (1)` misses ties; the task says "game(s)". See [Solutions](#p41--cheapest-games-overall) |
| ✅ | P4.2 | Games Ana has played | IN subquery | 🟢 Easy | 10 min / | 2026-10-26 | Correct. See [Solutions](#p42--games-ana-has-played) |
| 🟨 | P4.3 | Cheapest game in each genre | Correlated subquery | 🟡 Medium | 15 min / | | ❌ Attempt 2026-10-26: 6 rows, wrong columns. **Retry.** Compare with MIN per same genre. See [Solutions](#p43--cheapest-game-in-each-genre) |
| ✅ | P4.4 | Players with > 1000 points in any game, once | ANY / IN / EXISTS | 🟡 Medium | 15 min / | 2026-10-26 | Correct output, but over-complicated: `HAVING points > 1000` belongs in `WHERE`. See [Solutions](#p44--players-with--1000-points-in-any-game-once) |
| 🟨 | P4.5 | Scores above the average for the same game | Correlated subquery + AVG | 🟡 Medium | 15 min / | | ❌ Attempt 2026-10-26: only the average per game, no comparison. **Retry.** See [Solutions](#p45--scores-above-the-average-for-the-same-game) |
| ✅ | P4.6 | Games nobody has played yet | NOT EXISTS | 🟢 Easy | 10 min / | 2026-10-26 | Correct on the sample, used `NOT IN`; `NOT EXISTS` is safer if `game_id` can be NULL. See [Solutions](#p46--games-nobody-has-played-yet) |
| ⬜ | P4.7 | Rewrite P4.5 with a window function | AVG() OVER (PARTITION BY) | 🔴 Hard | 15 min / | | Skipped 2026-10-26 (not answered). **Retry** after P4.5. See the hint in [Solutions](#p47--rewrite-p45-with-a-window-function) |
| 🟨 | P4.8 | Top scorer(s) per game, including ties | RANK / correlated MAX | 🔴 Hard | 20 min / | | Close: `ROW_NUMBER()` drops game 4's tie (4 rows, expected 5). **Retry.** See [Solutions](#p48--top-scorers-per-game-including-ties) |

## 5. Common Records Between Tables

Source: `mock-interview-practical-scenarios.md`

| Status | # | Exercise | Topic | Level | Time (est / actual) | Date | Note |
|---|---|---|---|---|---|---|---|
| ⬜ | P5.1 | Names of students with ≥ 1 exam, two methods | JOIN + DISTINCT / IN | 🟢 Easy | 10 min / | | Expected: Ana · Luis |
| ⬜ | P5.2 | Each student with number of exams | JOIN + GROUP BY + COUNT | 🟡 Medium | 10 min / | | Expected: 1, Ana, 3 · 2, Luis, 1 |
| ⬜ | P5.3 | Students with no exam: NOT IN vs NOT EXISTS | NOT IN / NOT EXISTS | 🟡 Medium | 15 min / | | Explain why NOT EXISTS is safer |
| ⬜ | P5.4 | Name and subject together — which method works? | INNER JOIN | 🟡 Medium | 10 min / | | Only JOIN returns columns from both tables |

## 6. Window Functions and Gaps & Islands

Source: `mock-interview-practical-scenarios.md`

| Status | # | Exercise | Topic | Level | Time (est / actual) | Date | Note |
|---|---|---|---|---|---|---|---|
| ⬜ | P6.1 | Running revenue | SUM() OVER (ORDER BY) | 🟡 Medium | 15 min / | | Use ROWS frame explicitly |
| ⬜ | P6.2 | Gap between bookings | LAG + DATEDIFF | 🟡 Medium | 10 min / | | Order by golfer, then start |
| ⬜ | P6.3 | Suspicious double bookings (≤ 30 min apart) | LAG / self-comparison | 🔴 Hard | 20 min / | | Exclude cancelled bookings |
| ⬜ | P6.4 | Free time windows | Gaps & islands | 🔴 Hard | 30 min / | | Harder variant: local time (+10 min) |

## 7. Data Integrity and Constraints

Source: `mock-interview-practical-scenarios.md`

| Status | # | Exercise | Topic | Level | Time (est / actual) | Date | Note |
|---|---|---|---|---|---|---|---|
| ⬜ | P7.1 | Overbookings and wrong counters | LEFT JOIN + aggregation | 🟡 Medium | 15 min / | | Inner join misses tee time 6 |
| ⬜ | P7.2 | Fix the counters | Set-based UPDATE | 🟡 Medium | 10 min / | | After fix P7.1 returns 1 row |
| ⬜ | P7.3 | Add the guard (CHECK constraint) | CHECK, WITH NOCHECK, trusted constraints | 🟡 Medium | 15 min / | | Error 547; is_not_trusted |
| ⬜ | P7.4 | Idempotency key | UNIQUE constraint / index | 🟡 Medium | 10 min / | | Retry must not create a duplicate |

## 8. Transactions and Concurrency

Source: `mock-interview-practical-scenarios.md`

| Status | # | Exercise | Topic | Level | Time (est / actual) | Date | Note |
|---|---|---|---|---|---|---|---|
| ⬜ | P8.1 | Atomic booking procedure | TRY/CATCH, THROW, transactions | 🔴 Hard | 20 min / | | Needs two query windows |
| ⬜ | P8.2 | Prove the race | Race conditions, UPDLOCK | 🔴 Hard | 20 min / | | Naive version overbooks |
| ⬜ | P8.3 | Expire holds | Set-based UPDATE + OUTPUT | 🟡 Medium | 15 min / | | Running twice changes nothing |
| ⬜ | P8.4 | Blocking → RCSI → SNAPSHOT | Isolation levels | 🔴 Hard | 25 min / | | Two windows, one open transaction |
| ⬜ | P8.5 | Deadlock | Lock ordering | 🔴 Hard | 20 min / | | Fix: same table order in both windows |

## 9. Performance Tuning

Source: `mock-interview-practical-scenarios.md`

| Status | # | Exercise | Topic | Level | Time (est / actual) | Date | Note |
|---|---|---|---|---|---|---|---|
| ⬜ | P9.1 | Index from a query | Index design, STATISTICS IO | 🔴 Hard | 25 min / | | Do P7.2 first |
| ⬜ | P9.2 | Sargability drill | Sargable predicates | 🟢 Easy | 10 min / | | No functions on indexed columns |
| ⬜ | P9.3 | Parameter sniffing | Plan cache, RECOMPILE / OPTIMIZE FOR | 🔴 Hard | 20 min / | | Insert 50,000 rows for course 3 |
| ⬜ | P9.4 | Pagination | OFFSET/FETCH vs keyset | 🟡 Medium | 15 min / | | TeeTimeId must be in ORDER BY |

## 10. Time Zones, Bulk Loads and SQL Code Review

Source: `mock-interview-practical-scenarios.md`

| Status | # | Exercise | Topic | Level | Time (est / actual) | Date | Note |
|---|---|---|---|---|---|---|---|
| ⬜ | P10.1 | Utilization per local day | AT TIME ZONE, integer division | 🟡 Medium | 15 min / | | AT TIME ZONE needs Windows names |
| ⬜ | P10.2 | Bulk insert | OPENJSON / table-valued parameter | 🟡 Medium | 15 min / | | Compare with 500 single inserts |
| ⬜ | P10.3 | Review a SQL pull request | Code review: injection, joins, SELECT * | 🔴 Hard | 10 min / | | Dynamic SQL injection risk |

## 180. Common Records from Two Tables (section 11)

Source: `claude/180-common-records-two-tables.md`

| Status | # | Exercise | Topic | Level | Time (est / actual) | Date | Note |
|---|---|---|---|---|---|---|---|
| ⬜ | 180.1 | Names of students with ≥ 1 exam, each once | EXISTS / IN | 🟢 Easy | 5 min / | | Avoid JOIN duplicates |
| ⬜ | 180.2 | How many rows does Student JOIN Exam return? | INNER JOIN duplicates | 🟢 Easy | 3 min / | | Ana ×3 + Luis ×1 = 4 rows |
| ⬜ | 180.3 | Each student with number of exams | JOIN + GROUP BY + COUNT | 🟡 Medium | 8 min / | | Group by StudentID and Name |
| ⬜ | 180.4 | Students with no exam (two ways) | EXCEPT / NOT EXISTS | 🟡 Medium | 8 min / | | Avoid NOT IN; Oracle uses MINUS |
| ⬜ | 180.5 | Why does the 2-column vs 1-column INTERSECT fail? | INTERSECT rules | 🟢 Easy | 3 min / | | Same column count & compatible types |

## Session log

| Date | Exercises done | Time spent | Takeaway |
|---|---|---|---|
| 2026-10-26 | P3.1 · P3.2 (attempt) · P3.3 (AI-assisted) · P3.4 (AI-corrected) | 48 min (+1 min AI-assisted, not counted) | UNION ALL unpivot works but scans the table 4×; CROSS APPLY VALUES does it in 1 scan. Check the output columns against the expected table before calling it done |
| 2026-10-26 | P4.1–P4.8 (4 of 8 passed; P4.7 skipped) | 1 h 20 min (section total, assumed from "1.20") | Compare to an aggregate per group with a correlated subquery; for "including ties" use `RANK()`, not `ROW_NUMBER()` |

## Solutions

### P3.1 — Address Lines

2026-10-26 · 6 min · ✅ matches the expected output

**My solution**

```sql
with sumarry 
as
(
	select MemberID,1 as 'LineNumber',MailingAddressLine1 as 'AddressLine',city,State,Zipcode from Members
	union all
	select MemberID,2 as 'LineNumber',MailingAddressLine2 as 'AddressLine',city,State,Zipcode from Members
	union all
	select MemberID,3 as 'LineNumber',MailingAddressLine3 as 'AddressLine',city,State,Zipcode from Members
	union all
	select MemberID,4 as 'LineNumber',MailingAddressLine4 as 'AddressLine',city,State,Zipcode from Members

)
select * from sumarry where AddressLine is not null and MemberID is not null
order by MemberID,LineNumber
```

**Review**

- Correct: one row per non-null line, the `NULL` MemberID row is excluded, and the order is right. `MemberID IS NOT NULL` is needed here because the column is nullable.
- Typo in the CTE name: `sumarry` → `summary` (or a clearer name such as `AddressLines`).
- `1 as 'LineNumber'` (a string literal as the alias) is deprecated style. Use `AS LineNumber` or `AS [LineNumber]`.
- Empty strings get through `IS NOT NULL`. If the data can have `''` or spaces, use `NULLIF(LTRIM(RTRIM(AddressLine)), '') IS NOT NULL` (or `TRIM` on SQL Server 2017+).
- Name the columns instead of `SELECT *`, and match the expected casing (`City`, `ZipCode`).
- Cost: `UNION ALL` ×4 reads `Members` four times. The alternatives below read it once.

**Alternative: CROSS APPLY VALUES (one scan, the same idea as P3.3)**

```sql
SELECT m.MemberID, a.LineNumber, a.AddressLine, m.City, m.State, m.ZipCode
FROM Members AS m
CROSS APPLY (VALUES
    (1, m.MailingAddressLine1),
    (2, m.MailingAddressLine2),
    (3, m.MailingAddressLine3),
    (4, m.MailingAddressLine4)
) AS a (LineNumber, AddressLine)
WHERE a.AddressLine IS NOT NULL
  AND m.MemberID IS NOT NULL
ORDER BY m.MemberID, a.LineNumber;
```

**Alternative: UNPIVOT (drops NULLs by itself)**

```sql
SELECT u.MemberID,
       CAST(RIGHT(u.ColName, 1) AS INT) AS LineNumber,
       u.AddressLine, u.City, u.State, u.ZipCode
FROM Members
UNPIVOT (AddressLine FOR ColName IN
    (MailingAddressLine1, MailingAddressLine2, MailingAddressLine3, MailingAddressLine4)) AS u
WHERE u.MemberID IS NOT NULL
ORDER BY u.MemberID, LineNumber;
```

When to pick which: `UNPIVOT` is the shortest but always drops NULLs, needs the same data type in every column, and gets `LineNumber` from the column name. `CROSS APPLY VALUES` reads the table once, lets you set the line number yourself, can keep NULLs if you want them, and can unpivot several column groups at once. `UNION ALL` is the easiest to read but reads the table once per column.

### P3.2 — Contact Phone Numbers

2026-10-26 · 13 min · 🟨 not done yet: the `Email` column is missing from the output

**My solution**

```sql
with resume_cte as
(
	select CustomerID,	'Home' as 'PhoneType',Phone_Home as 'PhoneNumber',CustomerName  from CustomerContacts
	union all
	select CustomerID,	'Work' as 'PhoneType',Phone_Work as 'PhoneNumber',CustomerName  from CustomerContacts
	union all
	select CustomerID,	'Mobile' as 'PhoneType',Phone_Mobile as 'PhoneNumber',CustomerName  from CustomerContacts
	union all
	select CustomerID,	'Fax' as 'PhoneType',Phone_Fax as 'PhoneNumber',CustomerName  from CustomerContacts

)
select * from resume_cte
where PhoneNumber is not null AND PhoneNumber <> ''  AND CustomerID  is not null
```

**Review**

- Rows are right: the filter drops NULL phones, empty phones and the NULL `CustomerID` row, so it passes the "Test your filter" rows.
- **Output doesn't match:** the expected output is `CustomerID, PhoneType, PhoneNumber, CustomerName, Email`. `Email` is missing from all four `SELECT`s. The other columns are in the right order.
- Good improvement over P3.1: you filter empty strings. `PhoneNumber <> ''` is already false for NULL, so `is not null` is redundant (it's fine to keep it for readability).
- Spaces-only values like `'   '` also count as empty, because SQL Server ignores trailing spaces when it compares strings. So `<> ''` catches them too.
- No `ORDER BY`, so the row order isn't guaranteed. The problem doesn't ask for one, but the expected table is sorted by customer, then Home → Work → Mobile → Fax. Without a sort the `UNION ALL` usually returns all Home rows first, then all Work rows, and so on. Sorting by `PhoneType` alphabetically would give Fax, Home, Mobile, Work, so you need a number for the sort (see below).
- `'PhoneType'` (a string as the column name) is old style. Use `AS PhoneType`. The CTE name `resume_cte` doesn't say what it holds. Use something like `Phones`.
- Same cost as P3.1: four `UNION ALL` blocks read `CustomerContacts` four times.

**Fixed version (same approach)**

```sql
WITH Phones AS
(
    SELECT CustomerID, 1 AS SortOrder, 'Home'   AS PhoneType, Phone_Home   AS PhoneNumber, CustomerName, Email FROM CustomerContacts
    UNION ALL
    SELECT CustomerID, 2, 'Work',   Phone_Work,   CustomerName, Email FROM CustomerContacts
    UNION ALL
    SELECT CustomerID, 3, 'Mobile', Phone_Mobile, CustomerName, Email FROM CustomerContacts
    UNION ALL
    SELECT CustomerID, 4, 'Fax',    Phone_Fax,    CustomerName, Email FROM CustomerContacts
)
SELECT CustomerID, PhoneType, PhoneNumber, CustomerName, Email
FROM Phones
WHERE PhoneNumber <> ''
  AND CustomerID IS NOT NULL
ORDER BY CustomerID, SortOrder;
```

<details>
<summary><b>Alternative: CROSS APPLY VALUES</b> (spoiler: this solves P3.3)</summary>

```sql
SELECT c.CustomerID, p.PhoneType, p.PhoneNumber, c.CustomerName, c.Email
FROM CustomerContacts AS c
CROSS APPLY (VALUES
    (1, 'Home',   c.Phone_Home),
    (2, 'Work',   c.Phone_Work),
    (3, 'Mobile', c.Phone_Mobile),
    (4, 'Fax',    c.Phone_Fax)
) AS p (SortOrder, PhoneType, PhoneNumber)
WHERE p.PhoneNumber <> ''
  AND c.CustomerID IS NOT NULL
ORDER BY c.CustomerID, p.SortOrder;
```

</details>

### P3.3 — Phone Numbers with CROSS APPLY VALUES

2026-10-26 · 1 min · ✅ matches the expected output, but **solved with AI assistance, not independently**. The time doesn't count as a real attempt. Redo it from scratch later without help.

**My solution**

```sql
SELECT c.CustomerID, p.PhoneType, p.PhoneNumber, c.CustomerName, c.Email
FROM CustomerContacts AS c
CROSS APPLY (VALUES
    (1, 'Home',   c.Phone_Home),
    (2, 'Work',   c.Phone_Work),
    (3, 'Mobile', c.Phone_Mobile),
    (4, 'Fax',    c.Phone_Fax)
) AS p (SortOrder, PhoneType, PhoneNumber)
WHERE p.PhoneNumber IS NOT NULL
  AND p.PhoneNumber <> ''
  AND c.CustomerID IS NOT NULL
ORDER BY c.CustomerID, p.SortOrder;
```

**Review**

- Correct: the output columns match, `SortOrder` is used only in `ORDER BY` (the expected output doesn't include it), and rows come out Home → Work → Mobile → Fax per customer. Customer 103 and the `NULL` `CustomerID` row are filtered out.
- `c.CustomerID IS NOT NULL` is needed: `CustomerID` is `INT NULL` in this table (it's not a primary key) and the sample data has a `NULL` row.
- `p.PhoneNumber IS NOT NULL` is redundant, because `<> ''` is already false for `NULL`.
- Spaces-only phones like `'   '` are filtered too, because SQL Server ignores trailing spaces when it compares strings, so `'   ' <> ''` is false. Tabs or non-breaking spaces would still get through.
- Not done yet: the task also asks you to explain when you'd pick `UNION ALL`, `CROSS APPLY (VALUES ...)` or `UNPIVOT`. Practise saying that out loud when you redo it.
- This is the same query as the spoiler under P3.2's review. When you redo it, don't open that section first.

### P3.4 — Monthly Sales Import with CROSS APPLY VALUES

2026-10-26 · 29 min (target 15 min) · ✅ matches the expected output (run on the sample data), but the **final query is based on an AI correction**. It doesn't count as a clean unaided attempt. Redo it from scratch later without help.

First attempt mistakes, to watch for next time:

- Mixed the two `CASE` forms. Simple `CASE x WHEN value THEN ...` only compares for equality. For `>=` / `<` you need searched `CASE WHEN x >= y THEN ...`.
- Cast the wrong column.
- No `ORDER BY`.

**My solution (after the correction)**

```sql
WITH cte_final AS
(
    SELECT s.RepID, s.RepName, p.SortOrder, p.SalesMonth,
           TRY_CAST(p.SalesAmount AS DECIMAL(10,2)) AS SalesAmount,
           s.MonthlyTarget
    FROM SalesImport AS s
    CROSS APPLY (VALUES
        (1, 'Jan', s.Sales_Jan),
        (2, 'Feb', s.Sales_Feb),
        (3, 'Mar', s.Sales_Mar),
        (4, 'Apr', s.Sales_Apr)
    ) AS p (SortOrder, SalesMonth, SalesAmount)
    WHERE p.SalesAmount IS NOT NULL
      AND p.SalesAmount <> ''
	  AND s.RepID is not null
)
SELECT RepID, RepName, SalesMonth, SalesAmount,
       CASE
           WHEN SalesAmount >= MonthlyTarget THEN 'Met'
           WHEN SalesAmount <  MonthlyTarget THEN 'Missed'
       END AS Status
FROM cte_final
ORDER BY RepID, SortOrder;
```

**Review**

- Correct on the sample: right columns, Jan → Apr order using `SortOrder` (which isn't in the output), blanks dropped. Ana's April equals her target and comes out `Met`, because the check is `>=`.
- `s.RepID is not null` is needed: `RepID` is `INT NULL` in this staging table (not a primary key) and the sample has a `NULL` row.
- `p.SalesAmount IS NOT NULL` is redundant, because `<> ''` is already false for `NULL`. Spaces-only cells (`'   '`) are dropped too, because SQL Server ignores trailing spaces when it compares strings.
- `TRY_CAST` keeps the query from failing, but text that isn't a number (e.g. `'12,5x'`) passes the text filter and comes back as a row with a `NULL` `SalesAmount` and a `NULL` `Status`. Decide which you want. To drop those rows, filter on the converted value: `WHERE TRY_CAST(p.SalesAmount AS DECIMAL(10,2)) IS NOT NULL`. To report them, label them, e.g. `ELSE 'Invalid'`.
- Because of that, the second `WHEN` is useful: with `ELSE 'Missed'` instead, a bad value would be wrongly marked `Missed`.
- `cte_final` doesn't say what it holds. Use something like `MonthlySales`.
- Mixing tabs and spaces (the `s.RepID` line) makes the alignment uneven in some editors.

### Section 4 — Subqueries on Real Data (attempt 2026-10-26)

1 h 20 min for the whole section (assumed from "1.20"); no per-problem times. 4 of 8 passed. All queries were run on the section's sample data.

### P4.1 — Cheapest game(s) overall

✅ Correct on the sample.

```sql
select top(1)title,price from games
 order by price
```

- The task says "game(s)": if two games tie at the lowest price, `TOP (1)` returns only one. Use `TOP (1) WITH TIES` (keeps the `ORDER BY`) or `WHERE price = (SELECT MIN(price) FROM games)`. The second is the non-correlated subquery this section is practising.

### P4.2 — Games Ana has played

✅ Correct.

```sql
select title  from games
where game_id  in  (select game_id from scores where player_name = 'Ana'  )
```

- Clean `IN` subquery. `IN` also handles a game Ana played twice without duplicating it, which a `JOIN` wouldn't.

### P4.3 — Cheapest game in each genre

🟨 ❌ Wrong: 6 rows × 3 columns (`genre`, `Games`, `Cheapest`); expected 3 rows × `title, genre, price`. **Retry.**

```sql
select distinct ge.genre,
		g.title + ','+ cast(g.price as varchar(5)) as 'Games',
		(
			select top(1)title+ ',' + cast(price as varchar(20))  from games c
			where ge.genre  =c.genre
			order by price
		) Cheapest
from games ge
join  games g on ge.genre = g.genre
```

- The self-join gives every game in every genre, so all 6 games are listed, each with the genre's cheapest beside it. The task wants only the cheapest rows.
- Joining title and price into one string (`'Pixel Kart,15.00'`) loses the separate columns and the numeric type. Return `title`, `genre`, `price` as columns.
- `DISTINCT` was covering up duplicates from the join, which usually means the join isn't needed.
- Hint: keep only the rows where a game's price equals the lowest price **in its own genre**. That's a correlated subquery with `MIN`.

<details>
<summary><b>Corrected P4.3</b> (spoiler: open only after your retry)</summary>

```sql
SELECT g.title, g.genre, g.price
FROM games AS g
WHERE g.price = (SELECT MIN(g2.price) FROM games AS g2 WHERE g2.genre = g.genre);
```

</details>

### P4.4 — Players with > 1000 points in any game, once

✅ Correct output (Ana, Luis, Marco), but over-complicated.

```sql
with tmp
as(
select player_name,game_id,sum(points) as total
from scores
group by player_name,game_id,points
having  points > 1000
)
select distinct player_name from tmp
```

- `HAVING points > 1000` only works because `points` is in the `GROUP BY`. A filter on single rows belongs in `WHERE`. `HAVING` is for conditions on aggregates.
- Grouping by `points` makes `sum(points)` pointless, and `total` is never used, so the CTE isn't needed.
- Simple form: `SELECT DISTINCT player_name FROM scores WHERE points > 1000;`. The section topic also wants a subquery form, e.g. `WHERE 1000 < ANY (...)` or `EXISTS`. Try writing one.

### P4.5 — Scores above the average for the same game

🟨 ❌ Incomplete: returns 4 rows (`game_id`, average); expected 1 row: Luis, 1, 1500. **Retry.**

```sql
select game_id,avg(points) as total
from scores
group by game_id
```

- This is the right first step (the average per game), but the scores are never compared with it.
- The alias `total` is misleading for an average. Use `avg_points`.
- `AVG` of an `INT` column does integer division (e.g. the average of 1 and 2 is 1). It's harmless on this sample (1350 is exact), but use `AVG(points * 1.0)` when you need decimals.
- Hint: for each score, compare `points` with the average of **the same game**. That's a correlated subquery in `WHERE`.

<details>
<summary><b>Corrected P4.5</b> (spoiler: open only after your retry)</summary>

```sql
SELECT s.player_name, s.game_id, s.points
FROM scores AS s
WHERE s.points > (SELECT AVG(s2.points) FROM scores AS s2 WHERE s2.game_id = s.game_id);
```

</details>

### P4.6 — Games nobody has played yet

✅ Correct on the sample.

```sql
select title from games g
where  game_Id  not in(  select game_id from scores s )
```

- `NOT IN` returns **no rows at all** if the subquery returns a `NULL` (`x NOT IN (1, NULL)` is never true). `scores.game_id` is nullable, so `NOT EXISTS` is the safe habit: `WHERE NOT EXISTS (SELECT 1 FROM scores s WHERE s.game_id = g.game_id)`.
- The aliases `g` and `s` are declared but never used.

### P4.7 — Rewrite P4.5 with a window function

⬜ Skipped (not answered). **Retry** after P4.5.

- Hint: put `AVG(points) OVER (PARTITION BY game_id)` next to each score in a CTE or derived table, then filter on it in the outer query. You can't use a window function directly in `WHERE`.

### P4.8 — Top scorer(s) per game, including ties

🟨 Close but wrong on ties: 4 rows, expected 5. **Retry.**

```sql
with allo
as
(
select
		player_name,
		game_id,
		points,
		ROW_NUMBER() over (partition by game_id order by points desc) rank
from scores
)
select * from allo where rank = 1
```

- `ROW_NUMBER()` always numbers rows 1, 2, 3…, even for ties, so only one of game 4's two 2000-point scorers (Marco, Luis) gets `1`. Luis's game 4 row is lost.
- `SELECT *` also returns the helper column, so there are 4 columns. Expected is `player_name, game_id, points`.
- `rank` as an alias clashes with the `RANK()` function name. Use something like `rn`. `allo` doesn't say what the CTE holds.
- Hint: you need a numbering function that gives tied rows the same number.

<details>
<summary><b>Corrected P4.8</b> (spoiler: open only after your retry)</summary>

```sql
WITH Ranked AS
(
    SELECT player_name, game_id, points,
           RANK() OVER (PARTITION BY game_id ORDER BY points DESC) AS rnk
    FROM scores
)
SELECT player_name, game_id, points
FROM Ranked
WHERE rnk = 1;
```

</details>
