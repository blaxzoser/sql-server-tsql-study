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
6. [Full Mock Sessions (Timed, Mixed)](#6-full-mock-sessions-timed-mixed)

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

## 6. Full Mock Sessions (Timed, Mixed)

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

### Self-evaluation after any session

- [ ] My query ran without errors on the sample data in the Answer link.
- [ ] I finished inside the time budget (note how far over if not).
- [ ] I could name the edge case my query would miss (ties, `NULL`s, orphans, duplicates).
- [ ] I could explain my query's approach out loud in under a minute.
