# SQL Mock Interview: Question Bank

Practice questions for a SQL / T-SQL interview, **without answers**.
Try each one yourself first, then click **Answer** to check it in [`source-database/`](source-database/).

**Difficulty:** 🟢 Easy · 🟡 Medium · 🔴 Hard
**Total:** 117 questions

---

## Table of Contents

1. [Pattern Matching (LIKE)](#1-pattern-matching-like)
2. [Common Records from Two Tables](#2-common-records-from-two-tables)
3. [Local and Global Variables](#3-local-and-global-variables)
4. [Subqueries](#4-subqueries)
5. [Unpivoting and OR vs AND Logic](#5-unpivoting-and-or-vs-and-logic)
6. [Duplicates and Max per Group](#6-duplicates-and-max-per-group)
7. [Recursive CTEs: Employee Hierarchy](#7-recursive-ctes-employee-hierarchy)
8. [Query Optimization](#8-query-optimization)
9. [Mock Interview Sets (Timed)](#9-mock-interview-sets-timed)

---

## How to practice

- Answer **out loud** like in a real interview: definition → example → trap or edge case.
- For query questions, **write the SQL** before opening the answer.
- Change ⬜ to ✅ in the **Done** column when you can answer it without help.

---

## 1. Pattern Matching (LIKE)

Source: [186-pattern-matching-like.md](source-database/186-pattern-matching-like.md)

### Concepts

| Done | # | Level | Question | Answer |
|---|---|---|---|---|
| ⬜ | 1.1 | 🟢 | Which operator is used for pattern matching in SQL? | [Answer](source-database/186-pattern-matching-like.md#12-interview-questions--answers) |
| ⬜ | 1.2 | 🟢 | What is the difference between the `%` and `_` wildcards? | [Answer](source-database/186-pattern-matching-like.md#12-interview-questions--answers) |
| ⬜ | 1.3 | 🟢 | What is the difference between `=` and `LIKE`? | [Answer](source-database/186-pattern-matching-like.md#12-interview-questions--answers) |
| ⬜ | 1.4 | 🟢 | What is the opposite of `LIKE`? | [Answer](source-database/186-pattern-matching-like.md#12-interview-questions--answers) |
| ⬜ | 1.5 | 🟡 | How do you search for a literal `%` or `_` character? | [Answer](source-database/186-pattern-matching-like.md#6-matching-a-literal--or-_-escape) |
| ⬜ | 1.6 | 🟡 | Is `LIKE` case-sensitive? Does it depend on the database? | [Answer](source-database/186-pattern-matching-like.md#7-case-sensitivity) |
| ⬜ | 1.7 | 🟡 | Does `LIKE '%'` return all rows? What about rows where the column is `NULL`? | [Answer](source-database/186-pattern-matching-like.md#8-null-handling) |
| ⬜ | 1.8 | 🟡 | How do you search for a name that contains a single quote, like `o'brien`? | [Answer](source-database/186-pattern-matching-like.md#12-interview-questions--answers) |
| ⬜ | 1.9 | 🟡 | What can you use when you need more complex patterns than `LIKE` allows? | [Answer](source-database/186-pattern-matching-like.md#9-extra-wildcards-and-alternatives-by-database) |
| ⬜ | 1.10 | 🔴 | Which is faster, `LIKE 'a%'` or `LIKE '%a'`? Why? | [Answer](source-database/186-pattern-matching-like.md#10-performance-notes) |

### Write the query

| Done | # | Level | Question | Answer |
|---|---|---|---|---|
| ⬜ | 1.11 | 🟢 | Return students whose name starts with "a". | [Answer](source-database/186-pattern-matching-like.md#12-interview-questions--answers) |
| ⬜ | 1.12 | 🟢 | Return students whose name starts with "r". | [Answer](source-database/186-pattern-matching-like.md#13-practice-exercises) |
| ⬜ | 1.13 | 🟢 | Return students whose second letter is "a". | [Answer](source-database/186-pattern-matching-like.md#12-interview-questions--answers) |
| ⬜ | 1.14 | 🟢 | Return students whose name has exactly 4 characters. | [Answer](source-database/186-pattern-matching-like.md#12-interview-questions--answers) |
| ⬜ | 1.15 | 🟢 | Return students whose third letter is "m". | [Answer](source-database/186-pattern-matching-like.md#13-practice-exercises) |
| ⬜ | 1.16 | 🟡 | Return students whose name starts with a vowel. | [Answer](source-database/186-pattern-matching-like.md#12-interview-questions--answers) |
| ⬜ | 1.17 | 🟡 | Return students whose name has "i" as the second-to-last character. | [Answer](source-database/186-pattern-matching-like.md#13-practice-exercises) |
| ⬜ | 1.18 | 🟡 | Return students whose name has **at least** 5 characters. | [Answer](source-database/186-pattern-matching-like.md#13-practice-exercises) |
| ⬜ | 1.19 | 🟡 | Return students whose name does **not** contain the letter "a". | [Answer](source-database/186-pattern-matching-like.md#13-practice-exercises) |
| ⬜ | 1.20 | 🟡 | Return product codes that start with the literal text `EMP_`. | [Answer](source-database/186-pattern-matching-like.md#13-practice-exercises) |
| ⬜ | 1.21 | 🟡 | A row `(9, NULL)` is added to `Student`. How many rows does `WHERE studentname LIKE '%'` return, and why? | [Answer](source-database/186-pattern-matching-like.md#13-practice-exercises) |
| ⬜ | 1.22 | 🔴 | `WHERE studentname LIKE 'A%'` returns no rows in PostgreSQL but 4 rows in MySQL. Why? | [Answer](source-database/186-pattern-matching-like.md#13-practice-exercises) |

---

## 2. Common Records from Two Tables

Source: [sql-180-common-records-two-tables.md](source-database/sql-180-common-records-two-tables.md)

### Concepts

| Done | # | Level | Question | Answer |
|---|---|---|---|---|
| ⬜ | 2.1 | 🟢 | How do you fetch common records from two tables? Name four ways. | [Answer](source-database/sql-180-common-records-two-tables.md#2-the-four-solutions) |
| ⬜ | 2.2 | 🟢 | What is the opposite of `INTERSECT`? | [Answer](source-database/sql-180-common-records-two-tables.md#10-interview-questions--answers) |
| ⬜ | 2.3 | 🟢 | Does `INTERSECT` remove duplicates? | [Answer](source-database/sql-180-common-records-two-tables.md#10-interview-questions--answers) |
| ⬜ | 2.4 | 🟡 | Is a `JOIN` the same as `INTERSECT`? | [Answer](source-database/sql-180-common-records-two-tables.md#10-interview-questions--answers) |
| ⬜ | 2.5 | 🟡 | Why does my `JOIN` return more rows than `INTERSECT`? How do you make them match? | [Answer](source-database/sql-180-common-records-two-tables.md#4-join-without-distinct-vs-intersect--seeing-the-outcome) |
| ⬜ | 2.6 | 🟡 | What are the rules for using `INTERSECT` (columns, types, order)? | [Answer](source-database/sql-180-common-records-two-tables.md#10-interview-questions--answers) |
| ⬜ | 2.7 | 🟡 | How do `INTERSECT`, `JOIN`, `IN` and `EXISTS` treat `NULL` values? | [Answer](source-database/sql-180-common-records-two-tables.md#6-null-handling-in-detail) |
| ⬜ | 2.8 | 🟡 | Your database doesn't support `INTERSECT`. What do you do? | [Answer](source-database/sql-180-common-records-two-tables.md#10-interview-questions--answers) |
| ⬜ | 2.9 | 🟡 | You need the student's name **and** the exam subject. Which method do you use, and why? | [Answer](source-database/sql-180-common-records-two-tables.md#10-interview-questions--answers) |
| ⬜ | 2.10 | 🔴 | Which of the four methods is fastest? What does it depend on? | [Answer](source-database/sql-180-common-records-two-tables.md#8-performance-notes) |

### Write the query

| Done | # | Level | Question | Answer |
|---|---|---|---|---|
| ⬜ | 2.11 | 🟢 | Return the **names** of students who took at least one exam, each name once. | [Answer](source-database/sql-180-common-records-two-tables.md#11-practice-exercises) |
| ⬜ | 2.12 | 🟡 | How many rows does `SELECT s.Name FROM Student s JOIN Exam e ON s.StudentID = e.StudentID` return, and why? | [Answer](source-database/sql-180-common-records-two-tables.md#11-practice-exercises) |
| ⬜ | 2.13 | 🟡 | Return each student who took an exam, with the **number of exams** they took. | [Answer](source-database/sql-180-common-records-two-tables.md#11-practice-exercises) |
| ⬜ | 2.14 | 🟡 | Return students who have **not** taken any exam. Write it two ways. | [Answer](source-database/sql-180-common-records-two-tables.md#11-practice-exercises) |
| ⬜ | 2.15 | 🟡 | Why does `SELECT StudentID, Name FROM Student INTERSECT SELECT StudentID FROM Exam` fail? | [Answer](source-database/sql-180-common-records-two-tables.md#11-practice-exercises) |

---

## 3. Local and Global Variables

Source: [tsql-local-vs-global-variables.md](source-database/tsql-local-vs-global-variables.md)

### Concepts

| Done | # | Level | Question | Answer |
|---|---|---|---|---|
| ⬜ | 3.1 | 🟢 | What are local and global variables in T-SQL, and how are they different? | [Answer](source-database/tsql-local-vs-global-variables.md#2-improved-answer-say-this-in-an-interview) |
| ⬜ | 3.2 | 🟢 | What is the scope of a local variable? | [Answer](source-database/tsql-local-vs-global-variables.md#8-likely-follow-up-interview-questions) |
| ⬜ | 3.3 | 🟢 | What is the default value of a declared local variable? | [Answer](source-database/tsql-local-vs-global-variables.md#8-likely-follow-up-interview-questions) |
| ⬜ | 3.4 | 🟢 | Can you create your own global variable in SQL Server? | [Answer](source-database/tsql-local-vs-global-variables.md#8-likely-follow-up-interview-questions) |
| ⬜ | 3.5 | 🟡 | What is the difference between `SET` and `SELECT` for assigning variables? | [Answer](source-database/tsql-local-vs-global-variables.md#8-likely-follow-up-interview-questions) |
| ⬜ | 3.6 | 🟡 | What is the difference between `@@IDENTITY`, `SCOPE_IDENTITY()` and `IDENT_CURRENT('table')`? | [Answer](source-database/tsql-local-vs-global-variables.md#8-likely-follow-up-interview-questions) |
| ⬜ | 3.7 | 🟡 | Why might `@@ROWCOUNT` return 0 after an `UPDATE` that changed rows? | [Answer](source-database/tsql-local-vs-global-variables.md#8-likely-follow-up-interview-questions) |
| ⬜ | 3.8 | 🟡 | Can a variable be used inside dynamic SQL? How? | [Answer](source-database/tsql-local-vs-global-variables.md#8-likely-follow-up-interview-questions) |
| ⬜ | 3.9 | 🟡 | How can you share a value beyond a local variable's scope (across procedures or batches)? | [Answer](source-database/tsql-local-vs-global-variables.md#7-sharing-values-beyond-a-local-variables-scope) |
| ⬜ | 3.10 | 🔴 | Does `ROLLBACK` undo changes to variables and table variables? | [Answer](source-database/tsql-local-vs-global-variables.md#8-likely-follow-up-interview-questions) |
| ⬜ | 3.11 | 🔴 | Table variable vs temp table: when do you use each? | [Answer](source-database/tsql-local-vs-global-variables.md#8-likely-follow-up-interview-questions) |

### Write the code

| Done | # | Level | Question | Answer |
|---|---|---|---|---|
| ⬜ | 3.12 | 🟢 | Declare `@Today DATE` set to `GETDATE()` and print it as `yyyy-MM-dd`. | [Answer](source-database/tsql-local-vs-global-variables.md#9-practice-exercises) |
| ⬜ | 3.13 | 🟡 | Write an `UPDATE` that raises salaries by 5% for one department and prints the number of affected rows **correctly**. | [Answer](source-database/tsql-local-vs-global-variables.md#9-practice-exercises) |
| ⬜ | 3.14 | 🟡 | Show that a variable declared before `GO` is not available after it, then fix it with a `#temp` table. | [Answer](source-database/tsql-local-vs-global-variables.md#9-practice-exercises) |
| ⬜ | 3.15 | 🟡 | Create a procedure with an `OUTPUT` parameter that returns the highest salary in a given department. | [Answer](source-database/tsql-local-vs-global-variables.md#9-practice-exercises) |
| ⬜ | 3.16 | 🔴 | Insert into a table with an identity column **and a trigger**, then compare `@@IDENTITY` and `SCOPE_IDENTITY()`. | [Answer](source-database/tsql-local-vs-global-variables.md#9-practice-exercises) |
| ⬜ | 3.17 | 🔴 | Prove that `ROLLBACK` doesn't undo changes to a table variable. | [Answer](source-database/tsql-local-vs-global-variables.md#9-practice-exercises) |

---

## 4. Subqueries

Source: [sql-subquery-types.md](source-database/sql-subquery-types.md)

### Concepts

| Done | # | Level | Question | Answer |
|---|---|---|---|---|
| ⬜ | 4.1 | 🟢 | What are the types of subquery? | [Answer](source-database/sql-subquery-types.md#1-the-question) |
| ⬜ | 4.2 | 🟢 | How do you tell a correlated subquery from a non-correlated one? | [Answer](source-database/sql-subquery-types.md#5-how-to-tell-them-apart) |
| ⬜ | 4.3 | 🟡 | Which runs first, the inner or the outer query? | [Answer](source-database/sql-subquery-types.md#9-likely-follow-up-interview-questions) |
| ⬜ | 4.4 | 🟡 | What other ways can subqueries be classified (by what they return, by where they appear)? | [Answer](source-database/sql-subquery-types.md#6-other-ways-interviewers-classify-subqueries) |
| ⬜ | 4.5 | 🟡 | What is the difference between `IN` and `EXISTS`? | [Answer](source-database/sql-subquery-types.md#9-likely-follow-up-interview-questions) |
| ⬜ | 4.6 | 🟡 | Can a subquery be used in `INSERT`, `UPDATE` or `DELETE`? | [Answer](source-database/sql-subquery-types.md#9-likely-follow-up-interview-questions) |
| ⬜ | 4.7 | 🟡 | What are `ANY` and `ALL`? | [Answer](source-database/sql-subquery-types.md#9-likely-follow-up-interview-questions) |
| ⬜ | 4.8 | 🟡 | What is a CTE and how does it relate to subqueries? | [Answer](source-database/sql-subquery-types.md#9-likely-follow-up-interview-questions) |
| ⬜ | 4.9 | 🟡 | Why can `NOT IN` return no rows when the subquery contains a `NULL`? | [Answer](source-database/sql-subquery-types.md#7-common-mistakes) |
| ⬜ | 4.10 | 🔴 | Subquery vs `JOIN`: which is better? Are correlated subqueries always slow? | [Answer](source-database/sql-subquery-types.md#9-likely-follow-up-interview-questions) |
| ⬜ | 4.11 | 🔴 | What is the maximum nesting level of subqueries in SQL Server? | [Answer](source-database/sql-subquery-types.md#9-likely-follow-up-interview-questions) |
| ⬜ | 4.12 | 🔴 | How would you rewrite a correlated subquery for better performance? Give three options. | [Answer](source-database/sql-subquery-types.md#8-rewriting-correlated-subqueries-performance) |

### Write the query

| Done | # | Level | Question | Answer |
|---|---|---|---|---|
| ⬜ | 4.13 | 🟢 | List the cheapest game(s) overall. *(non-correlated)* | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| ⬜ | 4.14 | 🟢 | List the games that Ana has played. *(non-correlated with `IN`)* | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| ⬜ | 4.15 | 🟡 | List the cheapest game in each genre. *(correlated)* | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| ⬜ | 4.16 | 🟡 | List players who scored more than 1000 points in any game. Show each player once. | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| ⬜ | 4.17 | 🟡 | List scores that are above the average score for that same game. *(correlated)* | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| ⬜ | 4.18 | 🔴 | Rewrite question 4.17 with a window function. | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| ⬜ | 4.19 | 🔴 | Spot the bug: why does `WHERE EXISTS (SELECT 1 FROM scores WHERE game_id = game_id)` return every game? | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |

---

## 5. Unpivoting and OR vs AND Logic

Source: [sql_unpivoting_guide.md](source-database/sql_unpivoting_guide.md)

### Concepts

| Done | # | Level | Question | Answer |
|---|---|---|---|---|
| ⬜ | 5.1 | 🟢 | What is unpivoting? Explain wide format vs long format. | [Answer](source-database/sql_unpivoting_guide.md#what-is-unpivoting) |
| ⬜ | 5.2 | 🟢 | What is the difference between `UNION` and `UNION ALL`? Which one do you use to unpivot? | [Answer](source-database/sql_unpivoting_guide.md#union-vs-union-all) |
| ⬜ | 5.3 | 🟡 | Why can't you use a column alias in the `WHERE` clause? | [Answer](source-database/sql_unpivoting_guide.md#3-using-alias-in-where-clause) |
| ⬜ | 5.4 | 🟡 | You want to exclude rows where a value is `NULL` **or** empty. Do you combine the conditions with `OR` or `AND`? Why? | [Answer](source-database/sql_unpivoting_guide.md#or-vs-and-logic-in-sql) |
| ⬜ | 5.5 | 🟡 | What is De Morgan's Law and how does it apply to `WHERE` clauses? | [Answer](source-database/sql_unpivoting_guide.md#de-morgans-law) |
| ⬜ | 5.6 | 🔴 | Besides `UNION ALL`, what other ways can you unpivot data in SQL Server? | [Answer](source-database/sql_unpivoting_guide.md#alternative-approaches) |

### Write the query

| Done | # | Level | Question | Answer |
|---|---|---|---|---|
| ⬜ | 5.7 | 🟡 | A member table has `MailingAddressLine1` to `MailingAddressLine4`. Unpivot it so each non-null line is its own row: `MemberID`, `LineNumber` (1–4), `AddressLine`, `City`, `State`, `ZipCode`. Exclude `NULL` lines and `NULL` `MemberID`. | [Answer](source-database/sql_unpivoting_guide.md#problem-1-address-lines) |
| ⬜ | 5.8 | 🟡 | A customer table has `Phone_Home`, `Phone_Work`, `Phone_Mobile`, `Phone_Fax`. Unpivot it to `CustomerID`, `PhoneType`, `PhoneNumber`, `CustomerName`, `Email`. Exclude `NULL` **or empty** phones and `NULL` `CustomerID`. | [Answer](source-database/sql_unpivoting_guide.md#problem-2-contact-phone-numbers) |

---

## 6. Duplicates and Max per Group

Source: [tsql-duplicates-and-max-per-group.md](source-database/tsql-duplicates-and-max-per-group.md)

### Concepts

| Done | # | Level | Question | Answer |
|---|---|---|---|---|
| ⬜ | 6.1 | 🟢 | What is the difference between `WHERE` and `HAVING`? | [Answer](source-database/tsql-duplicates-and-max-per-group.md#where-vs-having) |
| ⬜ | 6.2 | 🟢 | What does `PARTITION BY` do? How is it different from `GROUP BY`? | [Answer](source-database/tsql-duplicates-and-max-per-group.md#1-core-concepts) |
| ⬜ | 6.3 | 🟡 | What is the logical processing order of a `SELECT` statement? | [Answer](source-database/tsql-duplicates-and-max-per-group.md#sql-logical-processing-order) |
| ⬜ | 6.4 | 🟡 | What is the difference between `ROW_NUMBER`, `RANK` and `DENSE_RANK`? | [Answer](source-database/tsql-duplicates-and-max-per-group.md#11-follow-up-questions-an-interviewer-might-ask) |
| ⬜ | 6.5 | 🟡 | Why can't you use a window function in the `WHERE` clause? | [Answer](source-database/tsql-duplicates-and-max-per-group.md#11-follow-up-questions-an-interviewer-might-ask) |
| ⬜ | 6.6 | 🟡 | How would you list the duplicate rows themselves (not just the keys)? | [Answer](source-database/tsql-duplicates-and-max-per-group.md#11-follow-up-questions-an-interviewer-might-ask) |
| ⬜ | 6.7 | 🟡 | How would you delete duplicates, keeping only one row of each? | [Answer](source-database/tsql-duplicates-and-max-per-group.md#11-follow-up-questions-an-interviewer-might-ask) |
| ⬜ | 6.8 | 🔴 | Two rows tie for the max value in a group. What happens with `ROW_NUMBER`, `RANK` and `TOP 1`? How do you control it? | [Answer](source-database/tsql-duplicates-and-max-per-group.md#9-edge-cases-ties-and-determinism) |
| ⬜ | 6.9 | 🔴 | GROUP BY + correlated subquery vs CTE + window functions: which is better for millions of rows, and what index helps? | [Answer](source-database/tsql-duplicates-and-max-per-group.md#10-approach-comparison) |

### Write the query (solve each one two ways: GROUP BY + subquery, and CTE + window function)

| Done | # | Level | Question | Answer |
|---|---|---|---|---|
| ⬜ | 6.10 | 🟡 | **Sales:** customers with more than one purchase, with total transactions, max amount, and the date of that max transaction. | [Answer](source-database/tsql-duplicates-and-max-per-group.md#3-exercise-1-sales-customers) |
| ⬜ | 6.11 | 🟡 | **Employee logins:** employees who logged in more than once, with total logins, earliest and latest login time, and the date of the first login. | [Answer](source-database/tsql-duplicates-and-max-per-group.md#4-exercise-2-employee-logins) |
| ⬜ | 6.12 | 🟡 | **Product orders:** products ordered more than once, with total orders, highest quantity in one order, and the date of that order. | [Answer](source-database/tsql-duplicates-and-max-per-group.md#5-exercise-3-product-orders) |
| ⬜ | 6.13 | 🔴 | **Page views:** returning users (more than one visit), with total visits, longest session (minutes), and the date of that session. | [Answer](source-database/tsql-duplicates-and-max-per-group.md#6-exercise-4-website-page-views) |
| ⬜ | 6.14 | 🔴 | **Deliveries:** customers with more than one delivery, with total deliveries, heaviest package (kg), and the date of that delivery. | [Answer](source-database/tsql-duplicates-and-max-per-group.md#7-exercise-5-package-deliveries) |

---

## 7. Recursive CTEs: Employee Hierarchy

Source: [sql-employee-hierarchy-exercise.md](source-database/sql-employee-hierarchy-exercise.md)

### Concepts

| Done | # | Level | Question | Answer |
|---|---|---|---|---|
| ⬜ | 7.1 | 🟡 | What is a recursive CTE? What are its two parts? | [Answer](source-database/sql-employee-hierarchy-exercise.md#-key-concepts-summary) |
| ⬜ | 7.2 | 🟡 | Why use `UNION ALL` instead of `UNION` in a recursive CTE? | [Answer](source-database/sql-employee-hierarchy-exercise.md#1-using-union-instead-of-union-all) |
| ⬜ | 7.3 | 🔴 | How do you avoid an infinite loop, and what is the recursion limit in SQL Server? | [Answer](source-database/sql-employee-hierarchy-exercise.md#scenario-3-deep-hierarchies) |
| ⬜ | 7.4 | 🔴 | What happens to employees whose `manager_id` points to a row that doesn't exist (orphans)? How do you find them? | [Answer](source-database/sql-employee-hierarchy-exercise.md#scenario-2-orphan-employees) |

### Write the query

| Done | # | Level | Question | Answer |
|---|---|---|---|---|
| ⬜ | 7.5 | 🟡 | Given `employees(employee_id, name, manager_id)`, return the full hierarchy with each employee's level (CEO = level 0). | [Answer](source-database/sql-employee-hierarchy-exercise.md#-solution) |
| ⬜ | 7.6 | 🔴 | Add the hierarchy path to each row, e.g. `CEO > VP > Manager > Employee`. | [Answer](source-database/sql-employee-hierarchy-exercise.md#variation-1-add-hierarchy-path) |
| ⬜ | 7.7 | 🔴 | Find all subordinates (direct and indirect) of a specific manager. | [Answer](source-database/sql-employee-hierarchy-exercise.md#variation-2-find-all-subordinates-of-a-specific-manager) |
| ⬜ | 7.8 | 🔴 | For each manager, count direct reports and total reports. | [Answer](source-database/sql-employee-hierarchy-exercise.md#variation-3-count-direct-and-total-reports) |

---

## 8. Query Optimization

Source: [sql-server-query-optimization-guide.md](source-database/sql-server-query-optimization-guide.md)

| Done | # | Level | Question | Answer |
|---|---|---|---|---|
| ⬜ | 8.1 | 🟡 | What is the difference between a Table Scan and an Index Seek? | [Answer](source-database/sql-server-query-optimization-guide.md#question-2-whats-the-difference-between-a-table-scan-and-an-index-seek) |
| ⬜ | 8.2 | 🟡 | Why is `SELECT *` considered a bad practice? | [Answer](source-database/sql-server-query-optimization-guide.md#common-anti-patterns-and-how-to-fix-them) |
| ⬜ | 8.3 | 🟡 | What does "sargable" mean? Why does `WHERE YEAR(OrderDate) = 2024` hurt performance? | [Answer](source-database/sql-server-query-optimization-guide.md#common-anti-patterns-and-how-to-fix-them) |
| ⬜ | 8.4 | 🟡 | What warning signs do you look for in an execution plan? | [Answer](source-database/sql-server-query-optimization-guide.md#analyzing-execution-plans) |
| ⬜ | 8.5 | 🔴 | How would you optimize a slow SQL query? Walk through your steps. | [Answer](source-database/sql-server-query-optimization-guide.md#question-1-how-would-you-optimize-a-slow-sql-query) |
| ⬜ | 8.6 | 🔴 | What are implicit conversions and why do they matter? Give a `VARCHAR` vs `NVARCHAR` example. | [Answer](source-database/sql-server-query-optimization-guide.md#question-3-what-are-implicit-conversions-and-why-do-they-matter) |
| ⬜ | 8.7 | 🔴 | What are statistics, why do they matter, and when do they go stale? | [Answer](source-database/sql-server-query-optimization-guide.md#statistics-the-hidden-performance-killer) |
| ⬜ | 8.8 | 🔴 | What is a covering index? Key columns vs `INCLUDE` columns? | [Answer](source-database/sql-server-query-optimization-guide.md#question-4-when-would-you-use-a-covering-index-vs-adding-columns-to-an-existing-index) |
| ⬜ | 8.9 | 🔴 | What questions do you ask before adding a new index? What is the trade-off? | [Answer](source-database/sql-server-query-optimization-guide.md#indexing-strategy-and-trade-offs) |
| ⬜ | 8.10 | 🔴 | How do you benchmark a query fairly? Why clear the buffer cache and plan cache, and where must you **never** do that? | [Answer](source-database/sql-server-query-optimization-guide.md#avoiding-cache-for-accurate-benchmarking) |
| ⬜ | 8.11 | 🔴 | Explain blocking vs deadlocks. | [Answer](source-database/sql-server-query-optimization-guide.md#question-5-explain-blocking-vs-deadlocks) |
| ⬜ | 8.12 | 🔴 | How do you find what is blocking a query in production? | [Answer](source-database/sql-server-query-optimization-guide.md#blocking-and-locking-in-production) |
| ⬜ | 8.13 | 🔴 | `NOLOCK` vs RCSI vs Snapshot isolation: what are the trade-offs? | [Answer](source-database/sql-server-query-optimization-guide.md#mitigation-strategies) |
| ⬜ | 8.14 | 🔴 | SQL Profiler vs Extended Events vs Query Store: what is each used for? | [Answer](source-database/sql-server-query-optimization-guide.md#profiling-tools) |

---

## 9. Mock Interview Sets (Timed)

Use these sets to simulate a full interview. Set a timer and don't open answers until the end.

### Set A: Junior (20 min)

1.1 · 1.2 · 1.14 · 2.1 · 2.11 · 3.1 · 3.5 · 4.1 · 5.2 · 6.1

### Set B: Mid-level (30 min)

1.7 · 2.5 · 2.14 · 3.6 · 4.5 · 4.9 · 4.15 · 5.4 · 6.4 · 6.10 · 7.5

### Set C: Senior (45 min)

1.10 · 3.10 · 4.12 · 6.8 · 6.13 · 7.6 · 8.3 · 8.5 · 8.6 · 8.8 · 8.11 · 8.13

### Self-evaluation after each set

- [ ] I gave a definition **and** an example for each concept question.
- [ ] I mentioned at least one trap or edge case (`NULL`, ties, duplicates, performance).
- [ ] My queries ran without errors on the sample data.
- [ ] I could explain **why** my query works, not only **what** it does.
