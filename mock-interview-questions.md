# SQL Mock Interview: Question Bank

Practice questions for a SQL / T-SQL interview, **without answers**.
Try each one yourself first, then click **Answer** to check it in [`source-database/`](source-database/).

**Difficulty:** 🟢 Easy · 🟡 Medium · 🔴 Hard
**Total:** 117 free-response questions + 40 multiple-choice

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
10. [Multiple-Choice Quiz](#10-multiple-choice-quiz)

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

---

## 10. Multiple-Choice Quiz

40 multiple-choice questions pulled from the topics above. Pick an option, then expand
**Show answer** to check yourself — the correct letter is hidden so it doesn't spoil the attempt.

### Pattern Matching (LIKE)

**MC1.** Which wildcard matches exactly one character in a `LIKE` pattern?

A) `%`
B) `_`
C) `*`
D) `?`

<details><summary>Show answer</summary>

**B) `_`** — `%` matches zero or more characters; `*` and `?` are filesystem wildcards, not SQL. See [186-pattern-matching-like.md](source-database/186-pattern-matching-like.md#2-the-two-wildcards).
</details>

---

**MC2.** `SELECT * FROM Student WHERE studentname LIKE 'ami_';` matches which of these?

A) `ami`
B) `amir`
C) `amirah`
D) `samir`

<details><summary>Show answer</summary>

**B) `amir`** — `ami` plus exactly one more character. `ami` is too short (the `_` requires one more char), `amirah` is too long, and `samir` doesn't start with `ami`. See [186-pattern-matching-like.md](source-database/186-pattern-matching-like.md#4-examples-and-their-outcome).
</details>

---

**MC3.** How do you search for a literal `%` character inside a value with `LIKE`?

A) Double it: `%%`
B) Use `ESCAPE` and escape the `%`
C) Use `=` instead of `LIKE`
D) It's not possible in SQL

<details><summary>Show answer</summary>

**B) Use `ESCAPE` and escape the `%`**, e.g. `LIKE '%50\%%' ESCAPE '\'`. See [186-pattern-matching-like.md](source-database/186-pattern-matching-like.md#6-matching-a-literal--or-_-escape).
</details>

---

**MC4.** `WHERE studentname LIKE '%'` on a row where `studentname` is `NULL`:

A) Always matches, because `%` means "anything"
B) Never matches `NULL`, because any comparison with `NULL` is unknown
C) Throws an error
D) Matches only if the column is a `VARCHAR`

<details><summary>Show answer</summary>

**B) Never matches `NULL`** — `LIKE` is a comparison, and any comparison against `NULL` evaluates to `UNKNOWN`, not `TRUE`. See [186-pattern-matching-like.md](source-database/186-pattern-matching-like.md#8-null-handling).
</details>

---

**MC5.** Which `LIKE` pattern is typically the **worst** for index usage on a large table?

A) `'abc%'`
B) `'%abc'`
C) `'a_c%'`
D) `'abc'` (no wildcard)

<details><summary>Show answer</summary>

**B) `'%abc'`** — a leading wildcard prevents an index seek on that column, forcing a scan. See [186-pattern-matching-like.md](source-database/186-pattern-matching-like.md#10-performance-notes).
</details>

### Common Records from Two Tables

**MC6.** Which of these does **not** return rows common to two tables?

A) `INTERSECT`
B) `INNER JOIN`
C) `EXCEPT`
D) `WHERE col IN (subquery)`

<details><summary>Show answer</summary>

**C) `EXCEPT`** — it returns rows in the first set that are **not** in the second (set difference), the opposite of what's needed. See [sql-180-common-records-two-tables.md](source-database/sql-180-common-records-two-tables.md#2-the-four-solutions).
</details>

---

**MC7.** Why can a plain `JOIN` return more rows than `INTERSECT` on the same two tables?

A) `JOIN` always sorts its output
B) `INTERSECT` ignores foreign keys
C) A matching row on one side can join to multiple matching rows on the other side, duplicating it
D) `INTERSECT` runs on a different thread

<details><summary>Show answer</summary>

**C)** — e.g. one student with three exam rows joins three times; `INTERSECT` naturally returns each matching row once. See [sql-180-common-records-two-tables.md](source-database/sql-180-common-records-two-tables.md#4-join-without-distinct-vs-intersect--seeing-the-outcome).
</details>

---

**MC8.** Does `INTERSECT` remove duplicate rows from its result?

A) No, it keeps all duplicates
B) Yes, it returns a distinct set
C) Only in SQL Server, not other databases
D) Only if `ORDER BY` is used

<details><summary>Show answer</summary>

**B) Yes** — like `UNION`, `INTERSECT` returns a distinct result set. See [sql-180-common-records-two-tables.md](source-database/sql-180-common-records-two-tables.md#10-interview-questions--answers).
</details>

---

**MC9.** Your database doesn't support `INTERSECT`. What's the most direct alternative?

A) There is none; you must upgrade
B) `INNER JOIN` or `WHERE ... IN (subquery)` / `EXISTS`
C) `UNION ALL` followed by `GROUP BY`
D) A `CROSS JOIN`

<details><summary>Show answer</summary>

**B)** — `INNER JOIN`, `IN`, or `EXISTS` all express "common records" without needing `INTERSECT`. See [sql-180-common-records-two-tables.md](source-database/sql-180-common-records-two-tables.md#10-interview-questions--answers).
</details>

### Local and Global Variables

**MC10.** What is the default value of a freshly declared local variable in T-SQL before it's assigned?

A) `0`
B) `''` (empty string)
C) `NULL`
D) It's undefined/garbage

<details><summary>Show answer</summary>

**C) `NULL`** — every declared local variable starts as `NULL` regardless of type. See [tsql-local-vs-global-variables.md](source-database/tsql-local-vs-global-variables.md#8-likely-follow-up-interview-questions).
</details>

---

**MC11.** Which statement correctly describes `SELECT` vs `SET` when assigning a variable?

A) They are 100% identical in every case
B) `SELECT` can assign from a query and silently take the last row if several are returned; `SET` requires a scalar expression and errors on multiple rows
C) `SET` can only assign string values
D) `SELECT` cannot assign variables at all

<details><summary>Show answer</summary>

**B)** — this is a classic trap: a `SELECT @v = col FROM table` with multiple matching rows won't error, it just assigns the last one processed. See [tsql-local-vs-global-variables.md](source-database/tsql-local-vs-global-variables.md#43-set-vs-select-for-assignment).
</details>

---

**MC12.** Which "global variable" reliably gives the identity value inserted by **your own session's last statement**, ignoring triggers?

A) `@@IDENTITY`
B) `SCOPE_IDENTITY()`
C) `IDENT_CURRENT('table')`
D) `@@ROWCOUNT`

<details><summary>Show answer</summary>

**B) `SCOPE_IDENTITY()`** — `@@IDENTITY` can return a value generated by a trigger in a different scope; `IDENT_CURRENT` is table-wide and session-independent. See [tsql-local-vs-global-variables.md](source-database/tsql-local-vs-global-variables.md#8-likely-follow-up-interview-questions).
</details>

---

**MC13.** Does a local variable declared in one batch (before `GO`) survive into the next batch?

A) Yes, always
B) No — `GO` ends the batch and the variable's scope with it
C) Only if it was declared with `DECLARE GLOBAL`
D) Only inside a stored procedure

<details><summary>Show answer</summary>

**B) No** — `GO` is a batch separator; local variable scope doesn't cross it. Use a `#temp` table or `SESSION_CONTEXT` to carry a value across batches. See [tsql-local-vs-global-variables.md](source-database/tsql-local-vs-global-variables.md#44-scope-ends-with-the-batch-go).
</details>

### Subqueries

**MC14.** What distinguishes a **correlated** subquery from a **non-correlated** one?

A) Correlated subqueries always use `EXISTS`
B) A correlated subquery references a column from the outer query, so it re-runs per outer row
C) Non-correlated subqueries can't appear in `WHERE`
D) There's no real difference, just style

<details><summary>Show answer</summary>

**B)** — the inner query depends on the outer row, conceptually executing once per outer row. See [sql-subquery-types.md](source-database/sql-subquery-types.md#5-how-to-tell-them-apart).
</details>

---

**MC15.** Which pair can return unexpected results when the subquery's column list includes a `NULL`?

A) `EXISTS` / `NOT EXISTS`
B) `IN` / `NOT IN`
C) `ANY` / `ALL` with `=`
D) `JOIN` / `LEFT JOIN`

<details><summary>Show answer</summary>

**B) `IN` / `NOT IN`** — `NOT IN (list containing NULL)` returns no rows at all, because the comparison against `NULL` makes the whole condition `UNKNOWN`. `NOT EXISTS` doesn't have this trap. See [sql-subquery-types.md](source-database/sql-subquery-types.md#mistake-2-not-in-with-null-values).
</details>

---

**MC16.** Which of these is **not** typically a valid way to classify subqueries?

A) Correlated vs non-correlated
B) Scalar vs row vs table subquery (by what they return)
C) By where they appear: `WHERE`, `FROM`, `SELECT`
D) By whether they use `UPPER()` or `LOWER()`

<details><summary>Show answer</summary>

**D)** — string-casing functions have nothing to do with how subqueries are classified. See [sql-subquery-types.md](source-database/sql-subquery-types.md#6-other-ways-interviewers-classify-subqueries).
</details>

---

**MC17.** `SELECT title FROM games WHERE EXISTS (SELECT 1 FROM scores WHERE game_id = game_id)` returns every game. Why?

A) `EXISTS` always returns every row
B) Both `game_id` references resolve to the `scores` table, so the condition isn't actually correlated to `games`
C) `SELECT 1` always means "all rows"
D) The outer query has a syntax error

<details><summary>Show answer</summary>

**B)** — the subquery needs `scores.game_id = games.game_id`; without the outer alias, both sides read from `scores`, making it `scores.game_id = scores.game_id`, always true. See [sql-subquery-types.md](source-database/sql-subquery-types.md#10-practice-exercises).
</details>

### Unpivoting and OR vs AND Logic

**MC18.** To exclude rows where a phone number is `NULL` **or** an empty string, which condition is correct?

A) `WHERE Phone IS NULL OR Phone = ''`
B) `WHERE Phone IS NOT NULL OR Phone <> ''`
C) `WHERE Phone IS NOT NULL AND Phone <> ''`
D) `WHERE Phone <> NULL AND Phone <> ''`

<details><summary>Show answer</summary>

**C)** — by De Morgan's law, excluding "A or B" means keeping rows where "not A **and** not B". Option A is the opposite (it would select the bad rows). See [sql_unpivoting_guide.md](source-database/sql_unpivoting_guide.md#or-vs-and-logic-in-sql).
</details>

---

**MC19.** When unpivoting four address columns into rows with `UNION ALL`, what's the most common mistake?

A) Using `UNION` instead of `UNION ALL`, which silently drops legitimate duplicate rows
B) Forgetting a semicolon
C) Using `ORDER BY` at all
D) Naming the output column `AddressLine`

<details><summary>Show answer</summary>

**A)** — `UNION` applies a distinct pass across all blocks, which can remove rows that happen to have identical values but represent different address lines. `UNION ALL` is what you want. See [sql_unpivoting_guide.md](source-database/sql_unpivoting_guide.md#1-duplicate-union-all-blocks).
</details>

---

**MC20.** Why can't you reference a `SELECT`-list alias (e.g. `AS PhoneNumber`) inside the same query's `WHERE` clause?

A) Aliases are only for display, never usable anywhere
B) `WHERE` is logically evaluated before `SELECT`, so the alias doesn't exist yet at that point
C) It's a SQL Server-only limitation
D) You can, it's just considered bad style

<details><summary>Show answer</summary>

**B)** — in logical query processing order, `WHERE` runs before `SELECT`, so column aliases defined in `SELECT` aren't available yet. See [sql_unpivoting_guide.md](source-database/sql_unpivoting_guide.md#3-using-alias-in-where-clause).
</details>

### Duplicates and Max per Group

**MC21.** What's the key difference between `WHERE` and `HAVING`?

A) They are interchangeable
B) `WHERE` filters rows before grouping; `HAVING` filters groups after aggregation
C) `HAVING` can only be used without `GROUP BY`
D) `WHERE` can use aggregate functions directly, `HAVING` cannot

<details><summary>Show answer</summary>

**B)** — `WHERE` operates on raw rows; `HAVING` operates on the results of `GROUP BY`/aggregates. See [tsql-duplicates-and-max-per-group.md](source-database/tsql-duplicates-and-max-per-group.md#where-vs-having).
</details>

---

**MC22.** For ties, which ranking function gives every tied row the **same** rank with **no gap** afterward?

A) `ROW_NUMBER()`
B) `RANK()`
C) `DENSE_RANK()`
D) `NTILE()`

<details><summary>Show answer</summary>

**C) `DENSE_RANK()`** — e.g. 1, 1, 2, 3 (no gap), while `RANK()` gives 1, 1, 3, 4 (gap after ties) and `ROW_NUMBER()` always gives unique values. See [tsql-duplicates-and-max-per-group.md](source-database/tsql-duplicates-and-max-per-group.md#11-follow-up-questions-an-interviewer-might-ask).
</details>

---

**MC23.** Why can't a window function like `ROW_NUMBER()` be used directly in a `WHERE` clause?

A) Window functions don't exist in T-SQL
B) `WHERE` is evaluated before window functions in logical query processing, so the value isn't computed yet
C) It's a permissions issue
D) You can, it just runs slowly

<details><summary>Show answer</summary>

**B)** — window functions are evaluated after `WHERE`/`GROUP BY`/`HAVING`, so you must wrap the query in a CTE or subquery and filter in the outer query. See [tsql-duplicates-and-max-per-group.md](source-database/tsql-duplicates-and-max-per-group.md#11-follow-up-questions-an-interviewer-might-ask).
</details>

---

**MC24.** Which approach generally scales better for finding the max-per-group row across millions of rows?

A) GROUP BY + correlated subquery per extra column
B) CTE + window function (single pass with `ROW_NUMBER()`)
C) Nested `SELECT *` three levels deep
D) A cursor looping row by row

<details><summary>Show answer</summary>

**B)** — the window-function approach typically does a single sorted pass, while a correlated subquery re-runs per group, and cursors are the slowest option of all. See [tsql-duplicates-and-max-per-group.md](source-database/tsql-duplicates-and-max-per-group.md#10-approach-comparison).
</details>

### Recursive CTEs

**MC25.** Why must a recursive CTE use `UNION ALL` rather than `UNION`?

A) `UNION` is not valid SQL syntax
B) SQL Server requires `UNION ALL` for recursive CTEs; plain `UNION` is not supported in the recursive part
C) `UNION ALL` is faster but both work identically
D) `UNION` reverses the row order

<details><summary>Show answer</summary>

**B)** — SQL Server's recursive CTE syntax requires `UNION ALL` between the anchor and recursive members. See [sql-employee-hierarchy-exercise.md](source-database/sql-employee-hierarchy-exercise.md#1-using-union-instead-of-union-all).
</details>

---

**MC26.** How do you lift SQL Server's default recursion limit for a deep hierarchy?

A) `SET RECURSION ON`
B) `OPTION (MAXRECURSION 0)` (or a higher number)
C) It cannot be changed
D) Add more `UNION ALL` branches

<details><summary>Show answer</summary>

**B) `OPTION (MAXRECURSION 0)`** disables the limit (0 = unlimited); a positive number sets a custom cap. See [sql-employee-hierarchy-exercise.md](source-database/sql-employee-hierarchy-exercise.md#scenario-3-deep-hierarchies).
</details>

---

**MC27.** An employee's `manager_id` points to a row that doesn't exist in the table (an orphan). What happens with a standard recursive-CTE hierarchy query using an inner join?

A) It throws a foreign-key error
B) The orphan silently doesn't appear in the results
C) It becomes a new root automatically
D) It causes infinite recursion

<details><summary>Show answer</summary>

**B)** — with a plain join, a row whose manager reference doesn't resolve simply has no match and is excluded; a `LEFT JOIN` variation is needed to surface orphans. See [sql-employee-hierarchy-exercise.md](source-database/sql-employee-hierarchy-exercise.md#scenario-2-orphan-employees).
</details>

### Query Optimization

**MC28.** What's the main problem with `SELECT *` in production queries?

A) It's a syntax error in some databases
B) It can prevent covering-index usage and pulls unnecessary columns, hurting I/O and plan quality
C) It automatically removes duplicate rows
D) It forces a table scan on every call, with no exceptions

<details><summary>Show answer</summary>

**B)** — `SELECT *` fetches every column, which can defeat a covering index and increase I/O, and it's fragile against schema changes. See [sql-server-query-optimization-guide.md](source-database/sql-server-query-optimization-guide.md#1-select-).
</details>

---

**MC29.** `WHERE YEAR(OrderDate) = 2024` is a classic performance anti-pattern because:

A) `YEAR()` doesn't exist in SQL Server
B) Wrapping the indexed column in a function makes the predicate non-sargable, usually forcing a scan
C) It returns the wrong year
D) It can only be used with `NVARCHAR` columns

<details><summary>Show answer</summary>

**B)** — applying a function to the column means the optimizer can't use an index seek on `OrderDate` directly; rewrite with a range: `OrderDate >= '2024-01-01' AND OrderDate < '2025-01-01'`. See [sql-server-query-optimization-guide.md](source-database/sql-server-query-optimization-guide.md#common-anti-patterns-and-how-to-fix-them).
</details>

---

**MC30.** What is the core difference between a **Table Scan** and an **Index Seek** in an execution plan?

A) They always cost the same
B) A scan reads every row (roughly O(n)); a seek navigates an index structure directly to matching rows
C) A seek is only possible without a `WHERE` clause
D) A scan can only happen on temp tables

<details><summary>Show answer</summary>

**B)** — a scan is a full read, useful when most rows qualify; a seek uses an index's structure to jump straight to the needed rows, better for selective queries. See [sql-server-query-optimization-guide.md](source-database/sql-server-query-optimization-guide.md#question-2-whats-the-difference-between-a-table-scan-and-an-index-seek).
</details>

---

**MC31.** Comparing a `VARCHAR` column to an `NVARCHAR` literal (or vice versa) can silently cause:

A) A syntax error
B) An implicit conversion that may prevent index usage on that column
C) Automatic index creation
D) The query to always return zero rows

<details><summary>Show answer</summary>

**B)** — due to data type precedence, SQL Server may convert the column instead of the literal, which can make the predicate non-sargable. See [sql-server-query-optimization-guide.md](source-database/sql-server-query-optimization-guide.md#varchar-vs-nvarchar).
</details>

---

**MC32.** What is a "covering index"?

A) An index that covers every table in the database
B) An index whose key and/or INCLUDE columns contain every column the query needs, avoiding a lookup
C) A clustered index, by definition
D) An index that disables statistics updates

<details><summary>Show answer</summary>

**B)** — when all needed columns are in the index (as key or `INCLUDE` columns), SQL Server can satisfy the query from the index alone, with no key/RID lookup. See [sql-server-query-optimization-guide.md](source-database/sql-server-query-optimization-guide.md#question-4-when-would-you-use-a-covering-index-vs-adding-columns-to-an-existing-index).
</details>

---

**MC33.** What's the practical difference between **blocking** and a **deadlock**?

A) They are the same thing
B) Blocking is one session waiting for another's lock to release; a deadlock is two (or more) sessions each waiting on a lock the other holds, which SQL Server detects and resolves by killing one
C) Deadlocks only happen with `SELECT` statements
D) Blocking always requires a server restart to clear

<details><summary>Show answer</summary>

**B)** — blocking resolves itself once the blocking transaction finishes; a deadlock is a cycle that never resolves on its own, so SQL Server picks a victim and rolls it back. See [sql-server-query-optimization-guide.md](source-database/sql-server-query-optimization-guide.md#question-5-explain-blocking-vs-deadlocks).
</details>

---

**MC34.** Why might statistics being "stale" hurt query performance?

A) Stale statistics make the database read-only
B) The optimizer relies on statistics to estimate row counts; if they're outdated, it can choose a bad plan (wrong join type, missing seek, etc.)
C) Statistics only affect backups
D) SQL Server refuses to run queries with stale statistics

<details><summary>Show answer</summary>

**B)** — outdated row/distribution estimates lead the optimizer to pick a plan suited for old data volumes, which can be very wrong for current data. See [sql-server-query-optimization-guide.md](source-database/sql-server-query-optimization-guide.md#statistics-the-hidden-performance-killer).
</details>

---

**MC35.** `NOLOCK` (read uncommitted) trades consistency for concurrency. What's the main risk?

A) It locks the whole database
B) It can return dirty, duplicate, or missing rows from data that is being modified concurrently
C) It permanently disables indexes
D) It's identical to Snapshot Isolation with no downsides

<details><summary>Show answer</summary>

**B)** — reading uncommitted data means you might see rows that will later be rolled back, or miss/duplicate rows during a page split. RCSI/Snapshot Isolation avoid blocking without this risk, at the cost of tempdb overhead. See [sql-server-query-optimization-guide.md](source-database/sql-server-query-optimization-guide.md#mitigation-strategies).
</details>

### Mixed Review

**MC36.** Which tool lets you persist captured query performance data across SQL Server restarts, built specifically for this purpose (unlike Profiler)?

A) SQL Server Profiler
B) Extended Events only
C) Query Store
D) `@@ROWCOUNT`

<details><summary>Show answer</summary>

**C) Query Store** — it persists plan and runtime statistics history in the database itself, across restarts; Profiler is legacy and session-based, Extended Events is lightweight tracing. See [sql-server-query-optimization-guide.md](source-database/sql-server-query-optimization-guide.md#profiling-tools).
</details>

---

**MC37.** Which SQL feature would you reach for to turn four phone-number columns into one `PhoneType`/`PhoneNumber` pair of rows per customer?

A) `PIVOT`
B) Unpivoting via `UNION ALL` (or `CROSS APPLY (VALUES ...)`)
C) `GROUP BY` alone
D) A recursive CTE

<details><summary>Show answer</summary>

**B)** — unpivoting (columns → rows) is the opposite of `PIVOT`; `UNION ALL` blocks or `CROSS APPLY (VALUES ...)` are the standard patterns. See [sql_unpivoting_guide.md](source-database/sql_unpivoting_guide.md#what-is-unpivoting).
</details>

---

**MC38.** For "customers with more than one purchase," which clause filters the grouped result to just those customers?

A) `WHERE COUNT(*) > 1`
B) `HAVING COUNT(*) > 1`
C) `ORDER BY COUNT(*) > 1`
D) `GROUP BY COUNT(*) > 1`

<details><summary>Show answer</summary>

**B) `HAVING COUNT(*) > 1`** — you can't use an aggregate directly in `WHERE`; `HAVING` filters after grouping. See [tsql-duplicates-and-max-per-group.md](source-database/tsql-duplicates-and-max-per-group.md#1-core-concepts).
</details>

---

**MC39.** Which statement about `EXISTS` vs `IN` is most accurate?

A) They can never be used interchangeably
B) `EXISTS` only checks for row existence and stops at the first match; it isn't tripped up by `NULL`s in the subquery's result the way `NOT IN` can be
C) `IN` is always faster than `EXISTS`
D) `EXISTS` cannot be correlated

<details><summary>Show answer</summary>

**B)** — `EXISTS`/`NOT EXISTS` evaluate truthiness of row existence and avoid the `NOT IN` + `NULL` trap; performance depends on indexes and data, not a fixed rule. See [sql-subquery-types.md](source-database/sql-subquery-types.md#9-likely-follow-up-interview-questions).
</details>

---

**MC40.** In a recursive CTE for an org chart, what defines the **anchor member**?

A) The recursive `UNION ALL` part that joins back to the CTE
B) The first query, typically selecting the root row(s) (e.g. `WHERE manager_id IS NULL`)
C) A `WHILE` loop around the CTE
D) The `OPTION (MAXRECURSION)` clause

<details><summary>Show answer</summary>

**B)** — the anchor member is the base case (e.g. employees with no manager); the recursive member then joins each new level back to it. See [sql-employee-hierarchy-exercise.md](source-database/sql-employee-hierarchy-exercise.md#-solution).
</details>
