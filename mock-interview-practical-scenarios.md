# SQL Mock Interview: Practical Scenarios

Full, self-contained coding exercises — each one is a complete scenario with its own
schema and sample data, the kind an interviewer gives you to solve live. **No answers
here**: write and run your SQL first, then open the **Answer** link to check yourself
against [`source-database/`](source-database/).

Unlike [mock-interview-questions.md](mock-interview-questions.md) (short concept/Q&A
questions), every exercise here is a **full scenario**: schema → requirement → your query.

---

## How to use this file

1. Read the scenario and write the `CREATE TABLE` / `INSERT` setup yourself from the
   description, or copy it from the **Answer** link's "Schema and sample data" section
   to save time — just don't peek at the solution below it yet.
2. Set a timer for the **suggested time**, and try to solve it solo first.
3. Only after you have a working query (or you time out), open the **Answer** link.
4. If you solved it, try the "harder variant" column too — most answer pages include one.

---

## Table of Contents

1. [Duplicates and Max Value per Group](#1-duplicates-and-max-value-per-group)
2. [Recursive Hierarchy](#2-recursive-hierarchy)
3. [Unpivoting Columns into Rows](#3-unpivoting-columns-into-rows)
4. [Subqueries on Real Data](#4-subqueries-on-real-data)
5. [Common Records Between Tables](#5-common-records-between-tables)

**New: tee-time booking database (from other interviews)**

6. [Window Functions and Gaps & Islands](#6-window-functions-and-gaps--islands)
7. [Data Integrity and Constraints](#7-data-integrity-and-constraints)
8. [Transactions and Concurrency](#8-transactions-and-concurrency)
9. [Performance Tuning](#9-performance-tuning)
10. [Time Zones, Bulk Loads and SQL Code Review](#10-time-zones-bulk-loads-and-sql-code-review)

**Practice**

11. [Full Mock Sessions (Timed, Mixed)](#11-full-mock-sessions-timed-mixed)

---

## Difficulty legend

| Level | Meaning |
|---|---|
| 🟢 Easy | One grouping/filter idea, single technique, small schema. |
| 🟡 Medium | Multiple columns derived from the same "extra info" row, or two tables. |
| 🔴 Hard | Window functions with ties, recursion, or several correct approaches to compare. |

---

## 1. Duplicates and Max Value per Group

Source: [tsql-duplicates-and-max-per-group.md](source-database/tsql-duplicates-and-max-per-group.md)
**Theme:** find groups that have more than one row, then pull the row with the max/min
value per group — the single most common "practical" SQL interview exercise.

**For every exercise below, solve it twice**, the way an interviewer will often ask:
- **Approach A:** `GROUP BY` + correlated subquery
- **Approach B:** CTE + window function (`ROW_NUMBER()` / `RANK()`)

| # | Scenario | Difficulty | Suggested time | Answer |
|---|---|---|---|---|
| P1.1 | **Sales (Customers).** A `Sales` table tracks transactions. Find customers who made more than one purchase, and for each show total number of transactions, the maximum transaction amount, and the date of that maximum transaction. | 🟡 Medium | 15 min | [Answer](source-database/tsql-duplicates-and-max-per-group.md#3-exercise-1-sales-customers) |
| P1.2 | **Employee Logins.** An `EmployeeLogins` table tracks logins. Return employees who logged in more than once, with total logins, earliest login time, latest login time, and the date of their first login. | 🟡 Medium | 15 min | [Answer](source-database/tsql-duplicates-and-max-per-group.md#4-exercise-2-employee-logins) |
| P1.3 | **Product Orders.** An `Orders` table tracks purchases. Find products ordered more than once; show total orders, the highest quantity in a single order, and the order date of that highest quantity. | 🟡 Medium | 15 min | [Answer](source-database/tsql-duplicates-and-max-per-group.md#5-exercise-3-product-orders) |
| P1.4 | **Website Page Views.** A `PageViews` table tracks visits. Find returning users (more than one visit), with total visits, longest session duration (minutes), and the date of that longest session. | 🔴 Hard | 20 min | [Answer](source-database/tsql-duplicates-and-max-per-group.md#6-exercise-4-website-page-views) |
| P1.5 | **Package Deliveries.** A `Deliveries` table tracks shipments. Find customers with more than one delivery, with total deliveries, heaviest package weight (kg), and the delivery date of that heaviest package. | 🔴 Hard | 20 min | [Answer](source-database/tsql-duplicates-and-max-per-group.md#7-exercise-5-package-deliveries) |

**Harder variant for any of the above (🔴 Hard, +10 min):** two rows tie for the max value
in a group — make your query return **all** tied rows instead of an arbitrary one, and
then make it return **exactly one** deterministic row instead. See
[Edge Cases: Ties and Determinism](source-database/tsql-duplicates-and-max-per-group.md#9-edge-cases-ties-and-determinism).

---

## 2. Recursive Hierarchy

Source: [sql-employee-hierarchy-exercise.md](source-database/sql-employee-hierarchy-exercise.md)
**Theme:** self-referencing tables (org charts, category trees, bill-of-materials) solved
with a recursive CTE.

| # | Scenario | Difficulty | Suggested time | Answer |
|---|---|---|---|---|
| P2.1 | Given `employees(employee_id, name, manager_id)`, write a query that returns the complete hierarchy showing each employee's **level** in the organization (CEO = level 0). | 🟡 Medium | 15 min | [Answer](source-database/sql-employee-hierarchy-exercise.md#-solution) |
| P2.2 | Extend P2.1 to also return a **hierarchy path** for each employee, e.g. `CEO > VP > Manager > Employee`. | 🔴 Hard | 15 min | [Answer](source-database/sql-employee-hierarchy-exercise.md#variation-1-add-hierarchy-path) |
| P2.3 | Given one manager's `employee_id`, find **all** of their subordinates, direct and indirect. | 🟡 Medium | 15 min | [Answer](source-database/sql-employee-hierarchy-exercise.md#variation-2-find-all-subordinates-of-a-specific-manager) |
| P2.4 | For every manager, count **direct** reports and **total** (direct + indirect) reports in the same result set. | 🔴 Hard | 20 min | [Answer](source-database/sql-employee-hierarchy-exercise.md#variation-3-count-direct-and-total-reports) |
| P2.5 | The table has **multiple root nodes** (more than one employee with `manager_id IS NULL`). Confirm your P2.1 query still works, and explain why. | 🟢 Easy | 5 min | [Answer](source-database/sql-employee-hierarchy-exercise.md#scenario-1-multiple-root-nodes) |
| P2.6 | Some employees have a `manager_id` that doesn't match any `employee_id` (orphans). Write a query that finds **only** the orphans. | 🟡 Medium | 10 min | [Answer](source-database/sql-employee-hierarchy-exercise.md#scenario-2-orphan-employees) |

---

## 3. Unpivoting Columns into Rows

Source: [sql_unpivoting_guide.md](source-database/sql_unpivoting_guide.md)
**Theme:** turning "wide" tables (one column per item) into "long" tables (one row per item) —
a common real-world data cleanup task.

| # | Scenario | Difficulty | Suggested time | Answer |
|---|---|---|---|---|
| P3.1 | **Address Lines.** Each member record has four separate columns: `MailingAddressLine1`–`4`. Unpivot so each non-null address line is its own row: output `MemberID`, `LineNumber` (1–4), `AddressLine`, `City`, `State`, `ZipCode`. Exclude `NULL` address lines and `NULL` `MemberID`. | 🟡 Medium | 20 min | [Answer](source-database/sql_unpivoting_guide.md#problem-1-address-lines) |
| P3.2 | **Contact Phone Numbers.** A customer table stores `Phone_Home`, `Phone_Work`, `Phone_Mobile`, `Phone_Fax`. Unpivot so each non-null, non-empty phone number is its own row: output `CustomerID`, `PhoneType`, `PhoneNumber`, `CustomerName`, `Email`. Exclude `NULL`/empty phones and `NULL` `CustomerID`. | 🟡 Medium | 20 min | [Answer](source-database/sql_unpivoting_guide.md#problem-2-contact-phone-numbers) |

**Harder variant (🔴 Hard, +10 min):** rewrite either P3.1 or P3.2 using
`CROSS APPLY (VALUES ...)` instead of stacked `UNION ALL` blocks, and explain when you'd
prefer one over the other. See [Alternative Approaches](source-database/sql_unpivoting_guide.md#alternative-approaches).

---

## 4. Subqueries on Real Data

Source: [sql-subquery-types.md](source-database/sql-subquery-types.md)
**Theme:** a small games/scores database used to practice correlated vs non-correlated
subqueries end to end.

| # | Scenario | Difficulty | Suggested time | Answer |
|---|---|---|---|---|
| P4.1 | List the cheapest game(s) overall. | 🟢 Easy | 10 min | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| P4.2 | List the games that a specific player ("Ana") has played. | 🟢 Easy | 10 min | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| P4.3 | List the cheapest game **in each genre**. | 🟡 Medium | 15 min | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| P4.4 | List players who have scored more than 1000 points in **any** game, showing each player once. | 🟡 Medium | 15 min | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| P4.5 | List scores that are above the **average score for that same game**. | 🟡 Medium | 15 min | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| P4.6 | List games that **nobody has played yet**. | 🟢 Easy | 10 min | [Answer](source-database/sql-subquery-types.md#example-3-games-nobody-has-played-yet-exists--not-exists) |
| P4.7 | Rewrite P4.5 using a window function instead of a correlated subquery. | 🔴 Hard | 15 min | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| P4.8 | Find the top scorer(s) for each game, handling ties (return all tied top scorers). | 🔴 Hard | 20 min | [Answer](source-database/sql-subquery-types.md#exactly-one-top-scorer-per-game-handles-ties) |

---

## 5. Common Records Between Tables

Source: [sql-180-common-records-two-tables.md](source-database/sql-180-common-records-two-tables.md)
**Theme:** a students/exams database used to compare `INTERSECT`, `JOIN`, `IN` and `EXISTS`
for the same question.

| # | Scenario | Difficulty | Suggested time | Answer |
|---|---|---|---|---|
| P5.1 | Return the **names** of students who have taken at least one exam, each name appearing once, using **two different methods** (e.g. `JOIN` with `DISTINCT` and `IN`). | 🟢 Easy | 10 min | [Answer](source-database/sql-180-common-records-two-tables.md#11-practice-exercises) |
| P5.2 | Return each student who took an exam, together with the **number of exams** they took. | 🟡 Medium | 10 min | [Answer](source-database/sql-180-common-records-two-tables.md#11-practice-exercises) |
| P5.3 | Return students who have **not** taken any exam — write it two ways (`NOT IN` and `NOT EXISTS`) and explain which is safer. | 🟡 Medium | 15 min | [Answer](source-database/sql-180-common-records-two-tables.md#11-practice-exercises) |
| P5.4 | You need the student's **name** and the exam's **subject** together in one result. Which of `INTERSECT` / `JOIN` / `IN` / `EXISTS` can actually do this, and why? Write the query. | 🟡 Medium | 10 min | [Answer](source-database/sql-180-common-records-two-tables.md#10-interview-questions--answers) |

---

## New: Tee-Time Booking Database (sections 6–10)

> These scenarios come from the SQL Server part of
> [forte-live-coding-mock-plan.md](source-other-interviews/forte-live-coding-mock-plan.md#6-mock-d--sql-server)
> and only cover topics that sections 1–5 don't (no more "max per group", joins or subquery drills).
>
> **Setup (once, ~5 min):** run the script in
> [§1.4 SQL schema + seed](source-other-interviews/forte-live-coding-mock-plan.md#14-sql-schema--seed-run-in-sql).
> It creates `Courses`, `TeeTimes`, `Golfers`, `Bookings` and `Payments` with about 20k tee times
> and 60k bookings. The seed is random and **deliberately broken**: some tee times are overbooked,
> 50 `BookedPlayers` counters are wrong, and there are no indexes beyond the primary keys and no
> check constraints. You fix those in the exercises.
>
> **Business rules used below:** a tee time holds `Capacity` players (4); the sum of players in
> non-cancelled bookings must never exceed it; a `Held` booking expires after 10 minutes.
>
> Turn on **Include Actual Execution Plan** and `SET STATISTICS IO, TIME ON` for every query.
> **Answer** links go to a written answer or hint; **Source** links go to the original exercise
> (not every exercise there has a full written answer, so check yourself by running it).

---

## 6. Window Functions and Gaps & Islands

**Theme:** comparing a row with its neighbours and finding consecutive runs, without self-joins or cursors.

| # | Scenario | Difficulty | Suggested time | Answer |
|---|---|---|---|---|
| P6.1 | **Running revenue.** Using `Payments` with `Status = 'Captured'`, show for each course and day the day's revenue and a **running total** for that course. Make sure two payments on the same day don't break the running total (`ROWS` vs `RANGE`). | 🟡 Medium | 15 min | [Source](source-other-interviews/forte-live-coding-mock-plan.md#d2--window-functions-25-min-) |
| P6.2 | **Gap between bookings.** For each booking, show the golfer, the tee time start, the golfer's **previous** tee time start, and the gap in days. The first booking of each golfer shows `NULL` (or `0` — say which and why). | 🟡 Medium | 10 min | [Answer](source-other-interviews/forte-live-coding-mock-plan.md#124-hints-for-the-tricky-exercises) |
| P6.3 | **Suspicious double bookings.** Find golfers with two non-cancelled bookings whose tee times are **within 30 minutes** of each other on the same day. Solve it with `LAG`, then with a self-join, and compare the plans. | 🔴 Hard | 20 min | [Answer](source-other-interviews/forte-live-coding-mock-plan.md#124-hints-for-the-tricky-exercises) |
| P6.4 | **Free time windows (gaps & islands).** For one course on one day, return each run of **consecutive** tee times (every 10 min) that has at least 2 free spots: start, end and number of slots, e.g. `08:00–08:40, 5 slots`. | 🔴 Hard | 30 min | [Answer](source-other-interviews/forte-live-coding-mock-plan.md#124-hints-for-the-tricky-exercises) |

**Harder variant (🔴 Hard, +10 min):** in P6.4, only return windows of at least 3 slots, and show
the times in the course's **local** time (see P10.1 for the time zone trap).

---

## 7. Data Integrity and Constraints

**Theme:** find bad data, fix it set-based, and then add constraints so it can't happen again.

| # | Scenario | Difficulty | Suggested time | Answer |
|---|---|---|---|---|
| P7.1 | **Overbookings and wrong counters.** (a) Find tee times where the sum of non-cancelled players is **greater than** `Capacity`. (b) Find tee times where `BookedPlayers` doesn't match that sum — include tee times with **no** active bookings but a non-zero counter. | 🟡 Medium | 15 min | [Source](source-other-interviews/forte-live-coding-mock-plan.md#d3--data-integrity-hunt-20-min-) |
| P7.2 | **Fix the counters.** Correct every wrong `BookedPlayers` with a **single** set-based `UPDATE ... FROM` (no cursor, no loop). Run P7.1(b) again: it must return 0 rows. | 🟡 Medium | 10 min | [Source](source-other-interviews/forte-live-coding-mock-plan.md#d3--data-integrity-hunt-20-min-) |
| P7.3 | **Add the guard.** Add `CHECK (BookedPlayers <= Capacity)`. First try it on the unfixed data and read the error; then try `WITH NOCHECK` and query `sys.check_constraints.is_not_trusted`; finally make it trusted. Explain why `WITH NOCHECK` is risky. | 🟡 Medium | 15 min | [Source](source-other-interviews/forte-live-coding-mock-plan.md#d4--atomic-booking-statement-20-min-) |
| P7.4 | **Idempotency key.** `Bookings.IdempotencyKey` is nullable but must be unique when set. Show that a `UNIQUE` constraint fails on the second `NULL`, then create a **filtered unique index** and prove that many `NULL`s are allowed but a repeated key is rejected. | 🟡 Medium | 10 min | [Source](source-other-interviews/forte-live-coding-mock-plan.md#d5--index-design-from-a-query-25-min-) |

---

## 8. Transactions and Concurrency

**Theme:** two golfers try to book the last spot at the same moment. You need two query windows (two sessions) for most of these.

| # | Scenario | Difficulty | Suggested time | Answer |
|---|---|---|---|---|
| P8.1 | **Atomic booking procedure.** Write `usp_BookTeeTime @TeeTimeId, @GolferId, @Players`: increment `BookedPlayers` only if capacity allows **in one statement**, check `@@ROWCOUNT`, insert the `Bookings` row in the same transaction, `THROW` a clear error when full, and use `SET NOCOUNT ON`, `SET XACT_ABORT ON` and `TRY/CATCH`. | 🔴 Hard | 20 min | [Answer](source-other-interviews/forte-live-coding-mock-plan.md#124-hints-for-the-tricky-exercises) |
| P8.2 | **Prove the race.** Write a naive version (read `BookedPlayers` into a variable, `IF` there's room, `WAITFOR DELAY '00:00:05'`, then `UPDATE`). Run it in two windows for the last spot and get an overbooking. Run P8.1 the same way and show it doesn't overbook. | 🔴 Hard | 20 min | [Source](source-other-interviews/forte-live-coding-mock-plan.md#d4--atomic-booking-statement-20-min-) |
| P8.3 | **Expire holds.** In one transaction, set every `Held` booking past `HoldExpiresUtc` to `Cancelled` and subtract its players from `TeeTimes.BookedPlayers`. Set-based only; use `OUTPUT` to capture the affected bookings. What if two jobs run it at the same time? | 🟡 Medium | 15 min | [Source](source-other-interviews/forte-live-coding-mock-plan.md#13-domain-model-use-in-net-sql-and-react) |
| P8.4 | **Blocking → RCSI → SNAPSHOT.** Session 1: `BEGIN TRAN; UPDATE TeeTimes SET Price = Price + 1 WHERE TeeTimeId = 1;` (don't commit). Session 2: read that row and watch it block. Turn on `READ_COMMITTED_SNAPSHOT` and repeat. Then use `SNAPSHOT` isolation in both sessions and produce an **update conflict**. Explain row versioning and its tempdb cost. | 🔴 Hard | 25 min | [Source](source-other-interviews/forte-live-coding-mock-plan.md#d8--isolation-levels--blocking-25-min) |
| P8.5 | **Deadlock.** Make a deadlock with two sessions that update `TeeTimes` and `Bookings` in **opposite order**. Find the deadlock graph in the `system_health` Extended Events session, name the victim and the resources, then fix it with a consistent access order. | 🔴 Hard | 20 min | [Source](source-other-interviews/forte-live-coding-mock-plan.md#d9--deadlock-20-min) |

---

## 9. Performance Tuning

**Theme:** measure first (logical reads + plan), change one thing, measure again.

| # | Scenario | Difficulty | Suggested time | Answer |
|---|---|---|---|---|
| P9.1 | **Index from a query.** The tee sheet query filters `TeeTimes` by `CourseId`, a UTC range on `StartUtc`, and `Capacity - BookedPlayers >= @minSpots`, ordered by `StartUtc`. Record reads and the plan, design the index (key order + `INCLUDE`), and measure again. Also index `Bookings(TeeTimeId)` and decide which FK columns need an index. | 🔴 Hard | 25 min | [Answer](source-other-interviews/forte-live-coding-mock-plan.md#124-hints-for-the-tricky-exercises) |
| P9.2 | **Sargability drill.** Rewrite so each one can seek: `WHERE CAST(StartUtc AS DATE) = '2026-11-10'` and `WHERE ISNULL(HoldExpiresUtc, '9999-12-31') < SYSUTCDATETIME()`. Compare the plans before and after. *(`YEAR()`, leading `%` and `N''` literals are already in [mock-interview-questions.md §8](mock-interview-questions.md#8-query-optimization).)* | 🟢 Easy | 10 min | [Source](source-other-interviews/forte-live-coding-mock-plan.md#d6--sargability-15-min) |
| P9.3 | **Parameter sniffing.** Create a procedure that returns bookings for `@CourseId`. Insert many extra bookings for one course so it is huge and the others are tiny. Run it for a tiny course first, then the huge one, and compare plans. Try `OPTION (RECOMPILE)`, `OPTIMIZE FOR`, and splitting into two procedures; explain the trade-offs. | 🔴 Hard | 20 min | [Source](source-other-interviews/forte-live-coding-mock-plan.md#d7--parameter-sniffing-20-min) |
| P9.4 | **Pagination.** List tee times ordered by `StartUtc, TeeTimeId`. Compare logical reads for `OFFSET/FETCH` page 1 vs page 2000, then write the **keyset** version and compare again. Why must `TeeTimeId` be in the `ORDER BY`? | 🟡 Medium | 15 min | [Source](source-other-interviews/forte-live-coding-mock-plan.md#d10--pagination-15-min) |

---

## 10. Time Zones, Bulk Loads and SQL Code Review

| # | Scenario | Difficulty | Suggested time | Answer |
|---|---|---|---|---|
| P10.1 | **Utilization per local day.** Per course and per **course-local** date, show tee times offered, players booked (non-cancelled) and utilization % (booked / capacity), ordered by utilization desc. Traps: `AT TIME ZONE` needs Windows names but `Courses.TimeZoneId` holds IANA names (`America/Chicago`); and integer division returns `0`. | 🟡 Medium | 15 min | [Source](source-other-interviews/forte-live-coding-mock-plan.md#d1--joins--aggregation-15-min--warm-up) |
| P10.2 | **Bulk insert.** Insert 500 tee times for a new course in **one** call: first from a JSON array parameter with `OPENJSON ... WITH (...)`, then with a table-valued parameter (`CREATE TYPE ... AS TABLE`). Compare with 500 single-row `INSERT`s (time and log usage). | 🟡 Medium | 15 min | [Source](source-other-interviews/forte-live-coding-mock-plan.md#d12--bulk-insert-15-min) |
| P10.3 | **Review a SQL pull request.** Review the `GetCourseBookings` procedure (dynamic SQL built by concatenating `@CourseName`, comma joins, `SELECT *`, `YEAR(t.StartUtc) = YEAR(GETDATE())`, no paging). List every issue with its impact, then rewrite it. Target: 7+ issues. | 🔴 Hard | 10 min | [Answer](source-other-interviews/forte-live-coding-mock-plan.md#123-e3--sql-pr-issues) |

---

## 11. Full Mock Sessions (Timed, Mixed)

Simulate a real 45–60 minute technical screen: pick one session, set a single timer for the
whole block, and don't look at any answer until time is up or you've submitted every query.

### Session A — Mid-level (45 min)

| Order | Exercise | Difficulty | Time budget |
|---|---|---|---|
| 1 | P5.1 | 🟢 Easy | 10 min |
| 2 | P1.1 (Approach A only) | 🟡 Medium | 15 min |
| 3 | P4.3 | 🟡 Medium | 15 min |
| 4 | P2.5 (quick reasoning, no coding) | 🟢 Easy | 5 min |

### Session B — Senior (60 min)

| Order | Exercise | Difficulty | Time budget |
|---|---|---|---|
| 1 | P1.4 (Approach A **and** B) | 🔴 Hard | 25 min |
| 2 | P2.2 | 🔴 Hard | 15 min |
| 3 | P4.8 | 🔴 Hard | 20 min |

### Session C — Data cleanup focus (40 min)

| Order | Exercise | Difficulty | Time budget |
|---|---|---|---|
| 1 | P3.1 | 🟡 Medium | 20 min |
| 2 | P3.2 | 🟡 Medium | 20 min |

### Session D — SQL Server concurrency (60 min)

Two query windows open, tee-time database loaded.

| Order | Exercise | Difficulty | Time budget |
|---|---|---|---|
| 1 | P8.1 | 🔴 Hard | 20 min |
| 2 | P8.2 | 🔴 Hard | 15 min |
| 3 | P8.5 | 🔴 Hard | 20 min |
| 4 | P7.3 (explain only, no coding) | 🟡 Medium | 5 min |

### Session E — Performance and data quality (60 min)

| Order | Exercise | Difficulty | Time budget |
|---|---|---|---|
| 1 | P7.1 + P7.2 | 🟡 Medium | 15 min |
| 2 | P9.1 | 🔴 Hard | 20 min |
| 3 | P6.4 | 🔴 Hard | 15 min |
| 4 | P10.3 | 🔴 Hard | 10 min |

### Self-evaluation after any session

- [ ] My query ran without errors on the sample data in the Answer link.
- [ ] I finished inside the time budget (note how far over if not).
- [ ] I could name the edge case my query would miss (ties, `NULL`s, orphans, duplicates, two sessions at once, time zones/DST).
- [ ] I could explain my query's approach out loud in under a minute.
