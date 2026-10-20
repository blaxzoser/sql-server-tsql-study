# SQL Mock Interview: Practical Scenarios

Full, self-contained coding exercises — each one is a complete scenario with its own
schema and sample data, the kind an interviewer gives you to solve live. **No answers
here**: write and run your SQL first, then open the **Answer** link to check yourself
against [`source-database/`](source-database/).

Unlike [mock-interview-questions.md](mock-interview-questions.md) (short concept/Q&A
questions), every exercise here is a **full scenario**: schema → requirement → your query.

---

## How to use this file

1. Run the **Schema and sample data** script shown with the exercise (sections 1–5), or the
   shared tee-time setup script once (sections 6–10). Your query must return the
   **Expected output** shown with each exercise. For the concurrency exercises (section 8), the
   expected output describes what each session should see.
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

| # | Scenario | Difficulty | Suggested time |
|---|---|---|---|
| [P1.1](#p11--sales-customers) | Sales (Customers) | 🟡 Medium | 15 min |
| [P1.2](#p12--employee-logins) | Employee Logins | 🟡 Medium | 15 min |
| [P1.3](#p13--product-orders) | Product Orders | 🟡 Medium | 15 min |
| [P1.4](#p14--website-page-views) | Website Page Views | 🔴 Hard | 20 min |
| [P1.5](#p15--package-deliveries) | Package Deliveries | 🔴 Hard | 20 min |

Each exercise below gives you the **table**, the **sample data** to load, and the **expected
output** your query must return. The solution is only behind the **Answer** link.

### P1.1 — Sales (Customers)

🟡 Medium · 15 min

**Task:** A `Sales` table tracks transactions. Find customers who made more than one purchase, and
for each show the total number of transactions, the maximum transaction amount, and the date of
that maximum transaction.

**Schema and sample data**

```sql
CREATE TABLE Sales (
    TransactionID INT,
    CustomerID INT,
    CustomerName VARCHAR(50),
    TransactionDate DATE,
    Amount DECIMAL(10,2)
);

INSERT INTO Sales VALUES
(1, 101, 'John Smith', '2024-01-15', 250.00),
(2, 102, 'Jane Doe',   '2024-01-16', 150.00),
(3, 101, 'John Smith', '2024-01-20', 400.00),
(4, 103, 'Bob Wilson', '2024-01-18', 300.00),
(5, 101, 'John Smith', '2024-02-01', 175.00),
(6, 104, 'Alice Brown','2024-01-22', 500.00),
(7, 102, 'Jane Doe',   '2024-02-05', 225.00),
(8, 103, 'Bob Wilson', '2024-02-10', 450.00);
```

**Expected output**

| CustomerID | CustomerName | TotalTransactions | MaxAmount | MaxTransactionDate |
|---|---|---|---|---|
| 101 | John Smith | 3 | 400.00 | 2024-01-20 |
| 102 | Jane Doe   | 2 | 225.00 | 2024-02-05 |
| 103 | Bob Wilson | 2 | 450.00 | 2024-02-10 |

Alice Brown is not in the result (only 1 transaction).

[Answer](source-database/tsql-duplicates-and-max-per-group.md#3-exercise-1-sales-customers)

### P1.2 — Employee Logins

🟡 Medium · 15 min

**Task:** An `EmployeeLogins` table tracks logins. Return employees who logged in more than once,
with total logins, the time of their **first** login, the time of their **last** login, and the
date of their first login. "First" and "last" are in time order (date, then time).

**Schema and sample data**

```sql
CREATE TABLE EmployeeLogins (
    LoginID INT,
    EmployeeID INT,
    EmployeeName VARCHAR(50),
    LoginDate DATE,
    LoginTime TIME,
    Department VARCHAR(30)
);

INSERT INTO EmployeeLogins VALUES
(1, 501, 'Sarah Chen',   '2024-12-15', '08:30:00', 'IT'),
(2, 502, 'Mike Johnson', '2024-12-15', '09:00:00', 'Sales'),
(3, 501, 'Sarah Chen',   '2024-12-15', '14:30:00', 'IT'),
(4, 503, 'Lisa Wong',    '2024-12-15', '08:45:00', 'HR'),
(5, 501, 'Sarah Chen',   '2024-12-15', '16:00:00', 'IT'),
(6, 502, 'Mike Johnson', '2024-12-16', '08:15:00', 'Sales'),
(7, 504, 'Tom Brown',    '2024-12-16', '09:30:00', 'Marketing'),
(8, 503, 'Lisa Wong',    '2024-12-16', '10:00:00', 'HR');
```

**Expected output**

| EmployeeID | EmployeeName | Department | TotalLogins | FirstLoginTime | LastLoginTime | FirstLoginDate |
|---|---|---|---|---|---|---|
| 501 | Sarah Chen   | IT    | 3 | 08:30:00 | 16:00:00 | 2024-12-15 |
| 502 | Mike Johnson | Sales | 2 | 09:00:00 | 08:15:00 | 2024-12-15 |
| 503 | Lisa Wong    | HR    | 2 | 08:45:00 | 10:00:00 | 2024-12-15 |

Tom Brown is not in the result (only 1 login). Watch Mike Johnson: his last login (2024-12-16)
is at 08:15, earlier in the day than his first one (09:00). `MIN(LoginTime)` / `MAX(LoginTime)`
would give him `08:15 / 09:00`, which is wrong: order by date, then time.

[Answer](source-database/tsql-duplicates-and-max-per-group.md#4-exercise-2-employee-logins)

### P1.3 — Product Orders

🟡 Medium · 15 min

**Task:** An `Orders` table tracks purchases. Find products ordered more than once; show total
orders, the highest quantity in a single order, and the order date of that highest quantity.

**Schema and sample data**

```sql
CREATE TABLE Orders (
    OrderID INT,
    ProductID INT,
    ProductName VARCHAR(50),
    OrderDate DATE,
    Quantity INT,
    UnitPrice DECIMAL(10,2)
);

INSERT INTO Orders VALUES
(1001, 201, 'Laptop',   '2024-11-10', 5,  899.99),
(1002, 202, 'Mouse',    '2024-11-12', 20, 25.50),
(1003, 201, 'Laptop',   '2024-11-15', 12, 899.99),
(1004, 203, 'Keyboard', '2024-11-14', 8,  45.00),
(1005, 201, 'Laptop',   '2024-11-20', 7,  899.99),
(1006, 204, 'Monitor',  '2024-11-18', 3,  299.99),
(1007, 202, 'Mouse',    '2024-11-22', 35, 25.50),
(1008, 203, 'Keyboard', '2024-11-25', 15, 45.00);
```

**Expected output**

| ProductID | ProductName | TotalOrders | MaxQuantity | MaxQuantityOrderDate |
|---|---|---|---|---|
| 201 | Laptop   | 3 | 12 | 2024-11-15 |
| 202 | Mouse    | 2 | 35 | 2024-11-22 |
| 203 | Keyboard | 2 | 15 | 2024-11-25 |

Monitor is not in the result (only 1 order).

[Answer](source-database/tsql-duplicates-and-max-per-group.md#5-exercise-3-product-orders)

### P1.4 — Website Page Views

🔴 Hard · 20 min

**Task:** A `PageViews` table tracks visits. Find returning users (more than one visit), with total
visits, longest session duration (minutes), and the date of that longest session.

**Schema and sample data**

```sql
CREATE TABLE PageViews (
    ViewID INT,
    UserID INT,
    UserName VARCHAR(50),
    VisitDate DATE,
    SessionDuration INT,   -- minutes
    PageCategory VARCHAR(30)
);

INSERT INTO PageViews VALUES
(1, 301, 'Emily Davis',  '2024-12-01', 45,  'Shopping'),
(2, 302, 'James Wilson', '2024-12-02', 15,  'News'),
(3, 301, 'Emily Davis',  '2024-12-03', 120, 'Shopping'),
(4, 303, 'Maria Garcia', '2024-12-02', 30,  'Entertainment'),
(5, 301, 'Emily Davis',  '2024-12-05', 60,  'Shopping'),
(6, 304, 'David Lee',    '2024-12-04', 90,  'Sports'),
(7, 302, 'James Wilson', '2024-12-06', 25,  'News'),
(8, 303, 'Maria Garcia', '2024-12-07', 75,  'Entertainment');
```

**Expected output**

| UserID | UserName | TotalVisits | LongestSession | LongestSessionDate |
|---|---|---|---|---|
| 301 | Emily Davis  | 3 | 120 | 2024-12-03 |
| 302 | James Wilson | 2 | 25  | 2024-12-06 |
| 303 | Maria Garcia | 2 | 75  | 2024-12-07 |

David Lee is not in the result (only 1 visit).

[Answer](source-database/tsql-duplicates-and-max-per-group.md#6-exercise-4-website-page-views)

### P1.5 — Package Deliveries

🔴 Hard · 20 min

**Task:** A `Deliveries` table tracks shipments. Find customers with more than one delivery, with
total deliveries, heaviest package weight (kg), and the delivery date of that heaviest package.

**Schema and sample data**

```sql
CREATE TABLE Deliveries (
    DeliveryID INT,
    CustomerID INT,
    CustomerName VARCHAR(50),
    DeliveryDate DATE,
    PackageWeight DECIMAL(5,2),   -- kg
    DeliveryStatus VARCHAR(20)
);

INSERT INTO Deliveries VALUES
(5001, 401, 'Chris Brown',   '2024-12-10', 5.50,  'Delivered'),
(5002, 402, 'Anna Taylor',   '2024-12-11', 2.30,  'Delivered'),
(5003, 401, 'Chris Brown',   '2024-12-12', 12.75, 'Delivered'),
(5004, 403, 'Kevin Zhang',   '2024-12-11', 8.00,  'Delivered'),
(5005, 401, 'Chris Brown',   '2024-12-15', 7.20,  'Delivered'),
(5006, 404, 'Sophie Martin', '2024-12-13', 3.80,  'Delivered'),
(5007, 402, 'Anna Taylor',   '2024-12-16', 9.50,  'Delivered'),
(5008, 403, 'Kevin Zhang',   '2024-12-18', 15.30, 'Delivered');
```

**Expected output**

| CustomerID | CustomerName | TotalDeliveries | HeaviestPackage | HeaviestPackageDate |
|---|---|---|---|---|
| 401 | Chris Brown | 3 | 12.75 | 2024-12-12 |
| 402 | Anna Taylor | 2 | 9.50  | 2024-12-16 |
| 403 | Kevin Zhang | 2 | 15.30 | 2024-12-18 |

Sophie Martin is not in the result (only 1 delivery).

[Answer](source-database/tsql-duplicates-and-max-per-group.md#7-exercise-5-package-deliveries)

**Harder variant for any of the above (🔴 Hard, +10 min):** two rows tie for the max value
in a group — make your query return **all** tied rows instead of an arbitrary one, and
then make it return **exactly one** deterministic row instead. See
[Edge Cases: Ties and Determinism](source-database/tsql-duplicates-and-max-per-group.md#9-edge-cases-ties-and-determinism).

---

## 2. Recursive Hierarchy

Source: [sql-employee-hierarchy-exercise.md](source-database/sql-employee-hierarchy-exercise.md)
**Theme:** self-referencing tables (org charts, category trees, bill-of-materials) solved
with a recursive CTE.

| # | Scenario | Difficulty | Suggested time |
|---|---|---|---|
| [P2.1](#p21--hierarchy-with-level) | Hierarchy with level | 🟡 Medium | 15 min |
| [P2.2](#p22--hierarchy-path) | Hierarchy path | 🔴 Hard | 15 min |
| [P2.3](#p23--all-subordinates-of-one-manager) | All subordinates of one manager | 🟡 Medium | 15 min |
| [P2.4](#p24--direct-and-total-reports) | Direct and total reports | 🔴 Hard | 20 min |
| [P2.5](#p25--multiple-root-nodes) | Multiple root nodes | 🟢 Easy | 5 min |
| [P2.6](#p26--orphan-employees) | Orphan employees | 🟡 Medium | 10 min |

### Schema and sample data (used by P2.1–P2.6)

```sql
CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY,
    Name VARCHAR(100),
    ManagerID INT NULL,
    CONSTRAINT FK_Employees_Manager FOREIGN KEY (ManagerID) REFERENCES Employees(EmployeeID)
);

INSERT INTO Employees (EmployeeID, Name, ManagerID) VALUES
(1, 'Alice', NULL),
(2, 'Bob', 1),
(3, 'Carol', 1),
(4, 'David', 2),
(5, 'Eve', 2),
(6, 'Frank', 3),
(7, 'Grace', 4);
```

The data as a tree:

```
Alice (CEO)
├── Bob
│   ├── David
│   │   └── Grace
│   └── Eve
└── Carol
    └── Frank
```

### P2.1 — Hierarchy with level

🟡 Medium · 15 min

**Task:** Return the complete hierarchy, showing each employee's **level** in the organization
(CEO = level 0).

**Expected output**

| EmployeeID | Name  | ManagerID | Level |
|---|---|---|---|
| 1 | Alice | NULL | 0 |
| 2 | Bob   | 1    | 1 |
| 3 | Carol | 1    | 1 |
| 4 | David | 2    | 2 |
| 5 | Eve   | 2    | 2 |
| 6 | Frank | 3    | 2 |
| 7 | Grace | 4    | 3 |

[Answer](source-database/sql-employee-hierarchy-exercise.md#-solution)

### P2.2 — Hierarchy path

🔴 Hard · 15 min

**Task:** Extend P2.1 to also return a **path** for each employee, from the CEO down to them.

**Expected output**

| EmployeeID | Name  | Level | Path |
|---|---|---|---|
| 1 | Alice | 0 | Alice |
| 2 | Bob   | 1 | Alice → Bob |
| 3 | Carol | 1 | Alice → Carol |
| 4 | David | 2 | Alice → Bob → David |
| 5 | Eve   | 2 | Alice → Bob → Eve |
| 6 | Frank | 2 | Alice → Carol → Frank |
| 7 | Grace | 3 | Alice → Bob → David → Grace |

[Answer](source-database/sql-employee-hierarchy-exercise.md#variation-1-add-hierarchy-path)

### P2.3 — All subordinates of one manager

🟡 Medium · 15 min

**Task:** Given one manager's `EmployeeID` (use Bob, `2`), return **all** of their subordinates,
direct and indirect, with how many levels below Bob they are. Don't include Bob himself.

**Expected output**

| EmployeeID | Name  | ManagerID | Level |
|---|---|---|---|
| 4 | David | 2 | 1 |
| 5 | Eve   | 2 | 1 |
| 7 | Grace | 4 | 2 |

[Answer](source-database/sql-employee-hierarchy-exercise.md#variation-2-find-all-subordinates-of-a-specific-manager)

### P2.4 — Direct and total reports

🔴 Hard · 20 min

**Task:** For every employee, count **direct** reports and **total** (direct + indirect) reports in
the same result.

**Expected output**

| EmployeeID | Name  | DirectReports | TotalReports |
|---|---|---|---|
| 1 | Alice | 2 | 6 |
| 2 | Bob   | 2 | 3 |
| 3 | Carol | 1 | 1 |
| 4 | David | 1 | 1 |
| 5 | Eve   | 0 | 0 |
| 6 | Frank | 0 | 0 |
| 7 | Grace | 0 | 0 |

[Answer](source-database/sql-employee-hierarchy-exercise.md#variation-3-count-direct-and-total-reports)

### P2.5 — Multiple root nodes

🟢 Easy · 5 min

**Task:** Add a second CEO with one report, run your P2.1 query again, and explain why it still works.

```sql
INSERT INTO Employees (EmployeeID, Name, ManagerID) VALUES
(8, 'Henry', NULL),
(9, 'Ivy', 8);
```

**Expected output:** the 7 rows from P2.1 plus:

| EmployeeID | Name  | ManagerID | Level |
|---|---|---|---|
| 8 | Henry | NULL | 0 |
| 9 | Ivy   | 8    | 1 |

[Answer](source-database/sql-employee-hierarchy-exercise.md#scenario-1-multiple-root-nodes)

### P2.6 — Orphan employees

🟡 Medium · 10 min

**Task:** Add an employee whose manager doesn't exist. The foreign key would block that, so turn it
off first. Then write a query that returns **only** the orphans. Does Jack appear in your P2.1 result?

```sql
ALTER TABLE Employees NOCHECK CONSTRAINT FK_Employees_Manager;

INSERT INTO Employees (EmployeeID, Name, ManagerID) VALUES
(10, 'Jack', 99);   -- there is no employee 99
```

**Expected output**

| EmployeeID | Name | ManagerID |
|---|---|---|
| 10 | Jack | 99 |

[Answer](source-database/sql-employee-hierarchy-exercise.md#scenario-2-orphan-employees)

---

## 3. Unpivoting Columns into Rows

Source: [sql_unpivoting_guide.md](source-database/sql_unpivoting_guide.md)
**Theme:** turning "wide" tables (one column per item) into "long" tables (one row per item) —
a common real-world data cleanup task.

| # | Scenario | Difficulty | Suggested time |
|---|---|---|---|
| [P3.1](#p31--address-lines) | Address Lines | 🟡 Medium | 20 min |
| [P3.2](#p32--contact-phone-numbers) | Contact Phone Numbers | 🟡 Medium | 20 min |

### P3.1 — Address Lines

🟡 Medium · 20 min

**Task:** Each member record has four separate columns, `MailingAddressLine1` to
`MailingAddressLine4`. Unpivot so each non-null address line is its own row. Output `MemberID`,
`LineNumber` (1–4), `AddressLine`, `City`, `State`, `ZipCode`. Exclude `NULL` address lines and
rows with a `NULL` `MemberID`. Order by `MemberID`, `LineNumber`.

**Schema and sample data** *(the source guide has no sample rows for this problem, so these were
written for this exercise)*

```sql
CREATE TABLE Members (
    MemberID INT NULL,
    MailingAddressLine1 VARCHAR(100),
    MailingAddressLine2 VARCHAR(100),
    MailingAddressLine3 VARCHAR(100),
    MailingAddressLine4 VARCHAR(100),
    City VARCHAR(50),
    State CHAR(2),
    ZipCode VARCHAR(10)
);

INSERT INTO Members VALUES
(1,    '100 Main St',   'Apt 4B',      NULL,        NULL,      'Austin', 'TX', '78701'),
(2,    'PO Box 77',     NULL,          NULL,        NULL,      'Denver', 'CO', '80202'),
(3,    'Attn: Billing', '500 Oak Ave', 'Suite 210', 'Floor 2', 'Miami',  'FL', '33101'),
(NULL, '9 Lost Rd',     NULL,          NULL,        NULL,      'Boise',  'ID', '83702');
```

**Expected output**

| MemberID | LineNumber | AddressLine | City | State | ZipCode |
|---|---|---|---|---|---|
| 1 | 1 | 100 Main St   | Austin | TX | 78701 |
| 1 | 2 | Apt 4B        | Austin | TX | 78701 |
| 2 | 1 | PO Box 77     | Denver | CO | 80202 |
| 3 | 1 | Attn: Billing | Miami  | FL | 33101 |
| 3 | 2 | 500 Oak Ave   | Miami  | FL | 33101 |
| 3 | 3 | Suite 210     | Miami  | FL | 33101 |
| 3 | 4 | Floor 2       | Miami  | FL | 33101 |

The Boise row is not in the result (its `MemberID` is `NULL`).

[Answer](source-database/sql_unpivoting_guide.md#problem-1-address-lines)

### P3.2 — Contact Phone Numbers

🟡 Medium · 20 min

**Task:** A customer table stores `Phone_Home`, `Phone_Work`, `Phone_Mobile`, `Phone_Fax`. Unpivot
so each phone number is its own row. Output `CustomerID`, `PhoneType` (`Home`/`Work`/`Mobile`/`Fax`),
`PhoneNumber`, `CustomerName`, `Email`. Exclude `NULL` **or empty** phones and rows with a `NULL`
`CustomerID`.

**Schema and sample data**

```sql
CREATE TABLE CustomerContacts (
    CustomerID INT NULL,
    CustomerName VARCHAR(50),
    Phone_Home VARCHAR(20),
    Phone_Work VARCHAR(20),
    Phone_Mobile VARCHAR(20),
    Phone_Fax VARCHAR(20),
    Email VARCHAR(100)
);

INSERT INTO CustomerContacts VALUES
(101, 'John Smith', '555-1234', NULL,       '555-5678', NULL,       'john@email.com'),
(102, 'Jane Doe',   NULL,       '555-9999', '555-8888', '555-7777', 'jane@email.com');
```

**Expected output**

| CustomerID | PhoneType | PhoneNumber | CustomerName | Email |
|---|---|---|---|---|
| 101 | Home   | 555-1234 | John Smith | john@email.com |
| 101 | Mobile | 555-5678 | John Smith | john@email.com |
| 102 | Work   | 555-9999 | Jane Doe   | jane@email.com |
| 102 | Mobile | 555-8888 | Jane Doe   | jane@email.com |
| 102 | Fax    | 555-7777 | Jane Doe   | jane@email.com |

**Test your filter:** add `(103, 'Empty Phones', '', NULL, '', NULL, 'x@email.com')` and
`(NULL, 'No Id', '555-0000', NULL, NULL, NULL, 'y@email.com')`. The expected output must not change.

[Answer](source-database/sql_unpivoting_guide.md#problem-2-contact-phone-numbers)

**Harder variant (🔴 Hard, +10 min):** rewrite either P3.1 or P3.2 using
`CROSS APPLY (VALUES ...)` instead of stacked `UNION ALL` blocks, and explain when you'd
prefer one over the other. See [Alternative Approaches](source-database/sql_unpivoting_guide.md#alternative-approaches).

---

## 4. Subqueries on Real Data

Source: [sql-subquery-types.md](source-database/sql-subquery-types.md)
**Theme:** a small games/scores database used to practice correlated vs non-correlated
subqueries end to end.

### Schema and sample data (used by P4.1–P4.8)

```sql
CREATE TABLE games (
    game_id INT PRIMARY KEY,
    title   VARCHAR(50),
    genre   VARCHAR(20),
    price   DECIMAL(6,2)
);

CREATE TABLE scores (
    score_id    INT PRIMARY KEY,
    game_id     INT,
    player_name VARCHAR(30),
    points      INT
);

INSERT INTO games VALUES
(1, 'Space Raiders', 'Shooter', 20.00),
(2, 'Dungeon Quest', 'RPG',     40.00),
(3, 'Pixel Kart',    'Racing',  15.00),
(4, 'Star Siege',    'Shooter', 35.00),
(5, 'Dragon Lore',   'RPG',     60.00),
(6, 'Turbo Drift',   'Racing',  25.00);

INSERT INTO scores VALUES
(1, 1, 'Ana',   1200),
(2, 1, 'Luis',  1500),
(3, 2, 'Ana',    800),
(4, 4, 'Marco', 2000),
(5, 4, 'Luis',  2000),
(6, 3, 'Marco',  500);
```

| # | Task | Difficulty | Time | Expected output | Answer |
|---|---|---|---|---|---|
| P4.1 | List the cheapest game(s) overall (`title`, `price`). | 🟢 Easy | 10 min | Pixel Kart, 15.00 | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| P4.2 | List the titles of the games that Ana has played. | 🟢 Easy | 10 min | Space Raiders · Dungeon Quest | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| P4.3 | List the cheapest game **in each genre** (`title`, `genre`, `price`). | 🟡 Medium | 15 min | Space Raiders, Shooter, 20.00 · Dungeon Quest, RPG, 40.00 · Pixel Kart, Racing, 15.00 | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| P4.4 | List players who scored more than 1000 points in **any** game, each player once. | 🟡 Medium | 15 min | Ana · Luis · Marco | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| P4.5 | List scores above the **average score for that same game** (`player_name`, `game_id`, `points`). | 🟡 Medium | 15 min | Luis, 1, 1500 | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| P4.6 | List the titles of games that **nobody has played yet**. | 🟢 Easy | 10 min | Dragon Lore · Turbo Drift | [Answer](source-database/sql-subquery-types.md#example-3-games-nobody-has-played-yet-exists--not-exists) |
| P4.7 | Rewrite P4.5 with a window function instead of a correlated subquery. | 🔴 Hard | 15 min | Same as P4.5: Luis, 1, 1500 | [Answer](source-database/sql-subquery-types.md#10-practice-exercises) |
| P4.8 | Find the top scorer(s) for each game, returning **all** tied top scorers (`player_name`, `game_id`, `points`). | 🔴 Hard | 20 min | see table below | [Answer](source-database/sql-subquery-types.md#exactly-one-top-scorer-per-game-handles-ties) |

**P4.8 expected output** (game 4 has a tie, so it appears twice):

| player_name | game_id | points |
|---|---|---|
| Luis  | 1 | 1500 |
| Ana   | 2 | 800 |
| Marco | 3 | 500 |
| Marco | 4 | 2000 |
| Luis  | 4 | 2000 |

---

## 5. Common Records Between Tables

Source: [sql-180-common-records-two-tables.md](source-database/sql-180-common-records-two-tables.md)
**Theme:** a students/exams database used to compare `INTERSECT`, `JOIN`, `IN` and `EXISTS`
for the same question.

### Schema and sample data (used by P5.1–P5.4)

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
```

Ana has 3 exams, Luis has 1, Maria has none.

**Expected output per exercise**

| # | Expected output |
|---|---|
| P5.1 | Ana · Luis (each once) |
| P5.2 | `1, Ana, 3` · `2, Luis, 1` (columns `StudentID`, `Name`, `ExamCount`) |
| P5.3 | `3, Maria` — the same from both queries |
| P5.4 | Ana, Math · Ana, Science · Ana, History · Luis, Math (columns `Name`, `Subject`) |

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
> The original plan uses a large **random** seed, so its results differ on every machine. To give
> every exercise a fixed **expected output**, this file uses the small dataset below instead
> (same tables and columns). Every expected output here was checked by running it on SQL Server.
> The **Answer** / **Source** links go to the original exercise or hint (not every exercise there
> has a full written answer).

### Business rules

- A tee time holds `Capacity` players (4). The sum of `Players` in **non-cancelled** bookings
  (`Held` + `Confirmed`) must never be more than `Capacity`.
- `TeeTimes.BookedPlayers` is a stored copy of that sum. In the sample data it is **wrong on
  purpose** for tee times 5, 6 and 9.
- A `Held` booking expires at `HoldExpiresUtc` if it isn't confirmed.
- All times are stored in **UTC**. `Courses.TimeZoneId` holds IANA names (`America/Chicago`).

### Schema and sample data (used by P6.1–P10.3)

Run this once in an empty database (`CREATE DATABASE TeeTimePractice;`). Sections 7 and 8 change
the data, so the expected outputs assume you do the exercises **in order**. To start again, drop
the five tables (`Payments`, `Bookings`, `Golfers`, `TeeTimes`, `Courses`) and run the script again.

```sql
CREATE TABLE Courses (
  CourseId   INT PRIMARY KEY,
  Name       NVARCHAR(100) NOT NULL,
  TimeZoneId VARCHAR(50)   NOT NULL,   -- IANA name, e.g. America/Chicago
  Country    CHAR(2)       NOT NULL,
  Currency   CHAR(3)       NOT NULL
);
CREATE TABLE TeeTimes (
  TeeTimeId     INT PRIMARY KEY,
  CourseId      INT NOT NULL REFERENCES Courses(CourseId),
  StartUtc      DATETIME2(0) NOT NULL,
  Capacity      TINYINT NOT NULL DEFAULT 4,
  BookedPlayers TINYINT NOT NULL DEFAULT 0,  -- stored counter; some rows are wrong on purpose
  Price         DECIMAL(8,2) NOT NULL,
  RowVersion    ROWVERSION
);
CREATE TABLE Golfers (
  GolferId  INT PRIMARY KEY,
  FirstName NVARCHAR(50) NOT NULL,
  LastName  NVARCHAR(50) NOT NULL,
  Email     VARCHAR(200) NOT NULL,
  IsMember  BIT NOT NULL
);
CREATE TABLE Bookings (
  BookingId      INT PRIMARY KEY,
  TeeTimeId      INT NOT NULL REFERENCES TeeTimes(TeeTimeId),
  GolferId       INT NOT NULL REFERENCES Golfers(GolferId),
  Players        TINYINT NOT NULL,
  Status         VARCHAR(10) NOT NULL,   -- Held / Confirmed / Cancelled
  CreatedUtc     DATETIME2(0) NOT NULL,
  HoldExpiresUtc DATETIME2(0) NULL,
  IdempotencyKey VARCHAR(64) NULL
);
CREATE TABLE Payments (
  PaymentId  INT PRIMARY KEY,
  BookingId  INT NOT NULL REFERENCES Bookings(BookingId),
  Amount     DECIMAL(8,2) NOT NULL,
  Currency   CHAR(3) NOT NULL,
  Status     VARCHAR(10) NOT NULL,   -- Pending / Captured / Failed / Refunded
  CreatedUtc DATETIME2(0) NOT NULL
);

INSERT INTO Courses VALUES
(1, 'Cedar Ridge GC', 'America/Chicago', 'US', 'USD'),
(2, 'Hollow Moor',    'Europe/London',   'UK', 'GBP');

-- Cedar Ridge: 8 slots every 10 min on 10 Nov + 1 slot on 11 Nov. Hollow Moor: 3 slots on 10 Nov.
INSERT INTO TeeTimes (TeeTimeId, CourseId, StartUtc, Capacity, BookedPlayers, Price) VALUES
( 1, 1, '2026-11-10 14:00', 4, 0, 50.00),
( 2, 1, '2026-11-10 14:10', 4, 1, 50.00),
( 3, 1, '2026-11-10 14:20', 4, 2, 50.00),
( 4, 1, '2026-11-10 14:30', 4, 4, 45.00),
( 5, 1, '2026-11-10 14:40', 4, 0, 45.00),   -- wrong counter
( 6, 1, '2026-11-10 14:50', 4, 2, 45.00),   -- wrong counter
( 7, 1, '2026-11-10 15:00', 4, 2, 40.00),
( 8, 1, '2026-11-10 15:10', 4, 0, 40.00),
( 9, 2, '2026-11-10 09:00', 4, 4, 55.00),   -- wrong counter
(10, 2, '2026-11-10 09:10', 4, 4, 55.00),
(11, 2, '2026-11-10 09:20', 4, 0, 55.00),
(12, 1, '2026-11-11 14:00', 4, 1, 50.00);

INSERT INTO Golfers VALUES
(1, 'Ana',   'Silva', 'ana@example.com',   1),
(2, 'Luis',  'Pinto', 'luis@example.com',  0),
(3, 'Marco', 'Rossi', 'marco@example.com', 1),
(4, 'Sofia', 'Lopez', 'sofia@example.com', 0);

INSERT INTO Bookings VALUES
( 1,  2, 2, 1, 'Confirmed', '2026-11-01 10:00', NULL, NULL),
( 2,  3, 1, 2, 'Confirmed', '2026-11-01 11:55', NULL, 'key-abc'),
( 3,  4, 2, 2, 'Confirmed', '2026-11-02 08:55', NULL, NULL),
( 4,  4, 3, 2, 'Confirmed', '2026-11-02 09:25', NULL, NULL),
( 5,  5, 4, 3, 'Confirmed', '2026-11-03 10:55', NULL, NULL),
( 6,  6, 3, 2, 'Cancelled', '2026-11-03 12:00', NULL, NULL),
( 7,  7, 4, 2, 'Held',      '2026-10-01 10:00', '2026-10-01 10:10', NULL),  -- hold already expired
( 8,  9, 1, 3, 'Confirmed', '2026-11-01 14:55', NULL, NULL),
( 9,  9, 3, 2, 'Confirmed', '2026-11-02 07:55', NULL, NULL),
(10, 12, 1, 1, 'Confirmed', '2026-11-05 09:00', NULL, NULL),
(11, 10, 4, 4, 'Held',      '2026-11-05 10:00', '2099-01-01 00:00', NULL);  -- hold still valid

INSERT INTO Payments VALUES
(1, 1,  50.00, 'USD', 'Captured', '2026-11-01 10:05'),
(2, 2, 100.00, 'USD', 'Captured', '2026-11-01 12:00'),
(3, 3,  90.00, 'USD', 'Captured', '2026-11-02 09:00'),
(4, 4,  90.00, 'USD', 'Failed',   '2026-11-02 09:30'),
(5, 5, 135.00, 'USD', 'Captured', '2026-11-03 11:00'),
(6, 8, 165.00, 'GBP', 'Captured', '2026-11-01 15:00'),
(7, 9, 110.00, 'GBP', 'Refunded', '2026-11-02 08:00');
```

What the data looks like (real players = sum of non-cancelled bookings):

| TeeTimeId | Course | StartUtc | Capacity | BookedPlayers (stored) | Real players |
|---|---|---|---|---|---|
| 1 | Cedar Ridge | 2026-11-10 14:00 | 4 | 0 | 0 |
| 2 | Cedar Ridge | 2026-11-10 14:10 | 4 | 1 | 1 |
| 3 | Cedar Ridge | 2026-11-10 14:20 | 4 | 2 | 2 |
| 4 | Cedar Ridge | 2026-11-10 14:30 | 4 | 4 | 4 |
| 5 | Cedar Ridge | 2026-11-10 14:40 | 4 | **0** | 3 |
| 6 | Cedar Ridge | 2026-11-10 14:50 | 4 | **2** | 0 |
| 7 | Cedar Ridge | 2026-11-10 15:00 | 4 | 2 | 2 (an expired hold) |
| 8 | Cedar Ridge | 2026-11-10 15:10 | 4 | 0 | 0 |
| 9 | Hollow Moor | 2026-11-10 09:00 | 4 | **4** | **5** (overbooked) |
| 10 | Hollow Moor | 2026-11-10 09:10 | 4 | 4 | 4 (a valid hold) |
| 11 | Hollow Moor | 2026-11-10 09:20 | 4 | 0 | 0 |
| 12 | Cedar Ridge | 2026-11-11 14:00 | 4 | 1 | 1 |

---

## 6. Window Functions and Gaps & Islands

**Theme:** comparing a row with its neighbours and finding consecutive runs, without self-joins or cursors.

| # | Scenario | Difficulty | Suggested time |
|---|---|---|---|
| [P6.1](#p61--running-revenue) | Running revenue | 🟡 Medium | 15 min |
| [P6.2](#p62--gap-between-bookings) | Gap between bookings | 🟡 Medium | 10 min |
| [P6.3](#p63--suspicious-double-bookings) | Suspicious double bookings | 🔴 Hard | 20 min |
| [P6.4](#p64--free-time-windows-gaps--islands) | Free time windows (gaps & islands) | 🔴 Hard | 30 min |

### P6.1 — Running revenue

🟡 Medium · 15 min

**Task:** Using only payments with `Status = 'Captured'`, show for each course and each payment day
(UTC date of `Payments.CreatedUtc`) the day's revenue and a **running total** for that course.
Course 1 has two payments on 2026-11-01: make sure they count as one day (`ROWS` vs `RANGE`).

**Expected output**

| CourseId | PayDate | DayRevenue | RunningTotal |
|---|---|---|---|
| 1 | 2026-11-01 | 150.00 | 150.00 |
| 1 | 2026-11-02 | 90.00  | 240.00 |
| 1 | 2026-11-03 | 135.00 | 375.00 |
| 2 | 2026-11-01 | 165.00 | 165.00 |

The `Failed` and `Refunded` payments are not counted.

[Source](source-other-interviews/forte-live-coding-mock-plan.md#d2--window-functions-25-min-)

### P6.2 — Gap between bookings

🟡 Medium · 10 min

**Task:** For each booking (all statuses), show the golfer, the tee time start, the golfer's
**previous** tee time start, and the gap in days (`DATEDIFF(DAY, ...)`). Order by golfer, then start.

**Expected output**

| BookingId | GolferId | StartUtc | PrevStartUtc | GapDays |
|---|---|---|---|---|
| 8  | 1 | 2026-11-10 09:00 | NULL | NULL |
| 2  | 1 | 2026-11-10 14:20 | 2026-11-10 09:00 | 0 |
| 10 | 1 | 2026-11-11 14:00 | 2026-11-10 14:20 | 1 |
| 1  | 2 | 2026-11-10 14:10 | NULL | NULL |
| 3  | 2 | 2026-11-10 14:30 | 2026-11-10 14:10 | 0 |
| 9  | 3 | 2026-11-10 09:00 | NULL | NULL |
| 4  | 3 | 2026-11-10 14:30 | 2026-11-10 09:00 | 0 |
| 6  | 3 | 2026-11-10 14:50 | 2026-11-10 14:30 | 0 |
| 11 | 4 | 2026-11-10 09:10 | NULL | NULL |
| 5  | 4 | 2026-11-10 14:40 | 2026-11-10 09:10 | 0 |
| 7  | 4 | 2026-11-10 15:00 | 2026-11-10 14:40 | 0 |

[Answer](source-other-interviews/forte-live-coding-mock-plan.md#124-hints-for-the-tricky-exercises)

### P6.3 — Suspicious double bookings

🔴 Hard · 20 min

**Task:** Find golfers with two **non-cancelled** bookings whose tee times are **30 minutes or less**
apart on the same day. Show both bookings. Solve it with `LAG`, then with a self-join, and compare
the plans.

**Expected output**

| GolferId | FirstBookingId | FirstStartUtc | SecondBookingId | SecondStartUtc | MinutesApart |
|---|---|---|---|---|---|
| 2 | 1 | 2026-11-10 14:10 | 3 | 2026-11-10 14:30 | 20 |
| 4 | 5 | 2026-11-10 14:40 | 7 | 2026-11-10 15:00 | 20 |

Marco's bookings 4 (14:30) and 6 (14:50) are not in the result: booking 6 is cancelled.

[Answer](source-other-interviews/forte-live-coding-mock-plan.md#124-hints-for-the-tricky-exercises)

### P6.4 — Free time windows (gaps & islands)

🔴 Hard · 30 min

**Task:** For Cedar Ridge (course 1) on 2026-11-10 (UTC), return each run of **consecutive** tee
times (every 10 min) with at least 2 free spots: start, end and number of slots. Use the **real**
players (sum of non-cancelled bookings), not the stored counter.

**Expected output**

| WindowStartUtc | WindowEndUtc | Slots |
|---|---|---|
| 2026-11-10 14:00 | 2026-11-10 14:20 | 3 |
| 2026-11-10 14:50 | 2026-11-10 15:10 | 3 |

Tee time 4 (full) and tee time 5 (1 free spot) break the run.

**Harder variant (🔴 Hard, +10 min):** show the windows in the course's **local** time. The first
window starts at `2026-11-10 08:00` in Chicago (UTC−6 in November). See P10.1 for the time zone trap.

[Answer](source-other-interviews/forte-live-coding-mock-plan.md#124-hints-for-the-tricky-exercises)

---

## 7. Data Integrity and Constraints

**Theme:** find bad data, fix it set-based, and then add constraints so it can't happen again.
Do these in order: each one starts from the data the previous one left.

| # | Scenario | Difficulty | Suggested time |
|---|---|---|---|
| [P7.1](#p71--overbookings-and-wrong-counters) | Overbookings and wrong counters | 🟡 Medium | 15 min |
| [P7.2](#p72--fix-the-counters) | Fix the counters | 🟡 Medium | 10 min |
| [P7.3](#p73--add-the-guard) | Add the guard | 🟡 Medium | 15 min |
| [P7.4](#p74--idempotency-key) | Idempotency key | 🟡 Medium | 10 min |

### P7.1 — Overbookings and wrong counters

🟡 Medium · 15 min

**Task:** In one query, list every tee time where the real players (sum of non-cancelled bookings)
are **more than** `Capacity`, **or** where `BookedPlayers` doesn't match the real players. Include
tee times with **no** active bookings but a non-zero counter.

**Expected output**

| TeeTimeId | Capacity | BookedPlayers | RealPlayers |
|---|---|---|---|
| 5 | 4 | 0 | 3 |
| 6 | 4 | 2 | 0 |
| 9 | 4 | 4 | 5 |

If tee time 6 is missing, you used an inner join: its only booking is cancelled, so it has no
active booking rows to join to.

[Source](source-other-interviews/forte-live-coding-mock-plan.md#d3--data-integrity-hunt-20-min-)

### P7.2 — Fix the counters

🟡 Medium · 10 min

**Task:** Correct every wrong `BookedPlayers` with a **single** set-based `UPDATE ... FROM` (no
cursor, no loop). Then run P7.1 again.

**Expected output**

- The `UPDATE` changes **3 rows** (`@@ROWCOUNT = 3`).
- New counters: tee time 5 → `3`, tee time 6 → `0`, tee time 9 → `5`.
- P7.1 now returns **one** row: `9, 4, 5, 5`. The counter is right, but tee time 9 is really
  overbooked. That is a business problem, not a counter problem.

[Source](source-other-interviews/forte-live-coding-mock-plan.md#d3--data-integrity-hunt-20-min-)

### P7.3 — Add the guard

🟡 Medium · 15 min

**Task:** Add `CONSTRAINT CK_Cap CHECK (BookedPlayers <= Capacity)` to `TeeTimes`. Then try
`WITH NOCHECK` and look at `sys.check_constraints`. Finally, decide how to make it trusted.

**Expected output**

1. Plain `ALTER TABLE TeeTimes ADD CONSTRAINT CK_Cap CHECK (...)` **fails** with error 547
   ("The ALTER TABLE statement conflicted with the CHECK constraint"), because of tee time 9.
   *(Before P7.2 it would have succeeded: the wrong counter of 4 hid the overbooking.)*
2. `ALTER TABLE TeeTimes WITH NOCHECK ADD CONSTRAINT CK_Cap ...` succeeds, and
   `SELECT name, is_not_trusted FROM sys.check_constraints` returns `CK_Cap | 1`.
3. To make it trusted: cancel one of tee time 9's bookings, fix its counter, then run
   `ALTER TABLE TeeTimes WITH CHECK CHECK CONSTRAINT CK_Cap;` → `is_not_trusted = 0`.

[Source](source-other-interviews/forte-live-coding-mock-plan.md#d4--atomic-booking-statement-20-min-)

### P7.4 — Idempotency key

🟡 Medium · 10 min

**Task:** `Bookings.IdempotencyKey` is nullable but must be unique when it has a value (only booking 2
has one: `'key-abc'`). Try a `UNIQUE` constraint, then create a **filtered unique index**, insert two
bookings with a `NULL` key and one with `'key-abc'`.

**Expected output**

1. `ALTER TABLE Bookings ADD CONSTRAINT UQ_Key UNIQUE (IdempotencyKey);` **fails** with error 1505:
   "duplicate key was found ... The duplicate key value is (&lt;NULL&gt;)". A `UNIQUE` constraint
   allows only one `NULL`.
2. `CREATE UNIQUE INDEX UX_Key ON Bookings(IdempotencyKey) WHERE IdempotencyKey IS NOT NULL;` succeeds.
3. Two new bookings with `NULL` keys insert fine.
4. A new booking with `'key-abc'` **fails** with error 2601: "Cannot insert duplicate key row ...
   The duplicate key value is (key-abc)".

Note: filtered indexes need `SET QUOTED_IDENTIFIER ON`. SSMS has it on; plain `sqlcmd` doesn't
(use `sqlcmd -I`), otherwise you get error 1934.

[Source](source-other-interviews/forte-live-coding-mock-plan.md#d5--index-design-from-a-query-25-min-)

---

## 8. Transactions and Concurrency

**Theme:** two golfers try to book the last spot at the same moment. You need **two query windows**
(two sessions) for P8.2, P8.4 and P8.5. Start from the data after section 7 (counters fixed).

| # | Scenario | Difficulty | Suggested time |
|---|---|---|---|
| [P8.1](#p81--atomic-booking-procedure) | Atomic booking procedure | 🔴 Hard | 20 min |
| [P8.2](#p82--prove-the-race) | Prove the race | 🔴 Hard | 20 min |
| [P8.3](#p83--expire-holds) | Expire holds | 🟡 Medium | 15 min |
| [P8.4](#p84--blocking--rcsi--snapshot) | Blocking → RCSI → SNAPSHOT | 🔴 Hard | 25 min |
| [P8.5](#p85--deadlock) | Deadlock | 🔴 Hard | 20 min |

### P8.1 — Atomic booking procedure

🔴 Hard · 20 min

**Task:** Write `usp_BookTeeTime @BookingId, @TeeTimeId, @GolferId, @Players`. It must increment
`BookedPlayers` only if capacity allows **in one statement**, check `@@ROWCOUNT`, insert a `Held`
booking (hold = 10 minutes) in the same transaction, `THROW` a clear error when full, and use
`SET NOCOUNT ON`, `SET XACT_ABORT ON` and `TRY/CATCH`.

**Expected behaviour**

| Call | Result |
|---|---|
| `EXEC usp_BookTeeTime 20, 1, 2, 2;` (tee time 1 is empty) | Succeeds. Tee time 1 `BookedPlayers` 0 → 2, booking 20 is `Held`. |
| `EXEC usp_BookTeeTime 21, 4, 1, 1;` (tee time 4 is full) | Your `THROW` error. No booking 21, tee time 4 stays at 4. |
| `EXEC usp_BookTeeTime 22, 1, 3, 3;` (only 2 spots left) | Your `THROW` error. Nothing changes. |
| `EXEC usp_BookTeeTime 23, 999, 3, 1;` (no such tee time) | An error that says the tee time doesn't exist (not "full"). |

[Answer](source-other-interviews/forte-live-coding-mock-plan.md#124-hints-for-the-tricky-exercises)

### P8.2 — Prove the race

🔴 Hard · 20 min

**Task:** Tee time 5 has 3 players and **1 spot** left. Write a naive version: read `BookedPlayers`
into a variable, `IF` there's room then `WAITFOR DELAY '00:00:05'`, then `UPDATE` and `INSERT`.
Start it in window 1 and, within 5 seconds, in window 2 (1 player each). Reset the data, then do
the same with `usp_BookTeeTime`.

**Expected behaviour**

| Version | Window 1 | Window 2 | Tee time 5 afterwards |
|---|---|---|---|
| Naive read-check-write | succeeds | succeeds | `BookedPlayers = 5` → **overbooked** |
| `usp_BookTeeTime` (P8.1) | succeeds | waits, then gets your "full" error | `BookedPlayers = 4` |

[Source](source-other-interviews/forte-live-coding-mock-plan.md#d4--atomic-booking-statement-20-min-)

### P8.3 — Expire holds

🟡 Medium · 15 min

**Task:** In one transaction, set every `Held` booking whose `HoldExpiresUtc` is in the past to
`Cancelled`, and subtract its players from `TeeTimes.BookedPlayers`. Set-based only; use `OUTPUT`
to capture the affected bookings. What happens if two jobs run it at the same time?

**Expected output** (the rows captured by `OUTPUT`)

| BookingId | TeeTimeId | Players |
|---|---|---|
| 7 | 7 | 2 |

Afterwards: booking 7 is `Cancelled`, tee time 7 `BookedPlayers` 2 → `0`. Booking 11 (hold until
2099) and tee time 10 (`4`) don't change. Running it a second time changes nothing.

[Source](source-other-interviews/forte-live-coding-mock-plan.md#13-domain-model-use-in-net-sql-and-react)

### P8.4 — Blocking → RCSI → SNAPSHOT

🔴 Hard · 25 min

**Task:**
1. Window 1: `BEGIN TRAN; UPDATE TeeTimes SET Price = Price + 1 WHERE TeeTimeId = 1;` (don't commit).
   Window 2: `SELECT Price FROM TeeTimes WHERE TeeTimeId = 1;`
2. Roll back, run `ALTER DATABASE TeeTimePractice SET READ_COMMITTED_SNAPSHOT ON WITH ROLLBACK IMMEDIATE;`
   and repeat step 1.
3. Run `ALTER DATABASE TeeTimePractice SET ALLOW_SNAPSHOT_ISOLATION ON;`. In **both** windows:
   `SET TRANSACTION ISOLATION LEVEL SNAPSHOT; BEGIN TRAN; SELECT Price FROM TeeTimes WHERE TeeTimeId = 1;`
   Then window 1 updates the price and commits; then window 2 updates the same row.

**Expected behaviour**

| Step | Window 2 |
|---|---|
| 1. Default `READ COMMITTED` | The `SELECT` **blocks** until window 1 commits or rolls back. |
| 2. RCSI on | The `SELECT` returns **at once** with the old price `50.00` (last committed version). |
| 3. `SNAPSHOT` | The `UPDATE` fails with **error 3960** (update conflict) and window 2's transaction is rolled back. |

[Source](source-other-interviews/forte-live-coding-mock-plan.md#d8--isolation-levels--blocking-25-min)

### P8.5 — Deadlock

🔴 Hard · 20 min

**Task:** Window 1: `BEGIN TRAN; UPDATE TeeTimes SET Price = Price WHERE TeeTimeId = 2;` then
`UPDATE Bookings SET Players = Players WHERE BookingId = 1;`. Window 2 does the same two updates in
the **opposite order** (`Bookings` first, then `TeeTimes`). Run the first update in each window, then
the second update in each window. Find the deadlock graph in the `system_health` Extended Events
session, then fix it.

**Expected behaviour**

- One window fails with **error 1205** ("Transaction ... was deadlocked ... and has been chosen as
  the deadlock victim"); the other one finishes.
- The graph (`xml_deadlock_report` in `system_health`) shows **2 processes** and **2 key locks**:
  one on `TeeTimes` and one on `Bookings`, each held by one process and requested by the other.
- After the fix (both windows update `TeeTimes` first, then `Bookings`), window 2 just **waits**
  for window 1 and then finishes: no error 1205.

[Source](source-other-interviews/forte-live-coding-mock-plan.md#d9--deadlock-20-min)

---

## 9. Performance Tuning

**Theme:** measure first (logical reads + plan), change one thing, measure again. Start from the data
after section 7 (counters fixed). Turn on **Include Actual Execution Plan** and
`SET STATISTICS IO, TIME ON`.

> On only 12 tee times the optimizer may still choose a scan even with a good index, because the
> whole table fits in one page. The **rows** below are fixed; to see real seek vs scan differences,
> also load the large random seed from
> [§1.4](source-other-interviews/forte-live-coding-mock-plan.md#14-sql-schema--seed-run-in-sql).

| # | Scenario | Difficulty | Suggested time |
|---|---|---|---|
| [P9.1](#p91--index-from-a-query) | Index from a query | 🔴 Hard | 25 min |
| [P9.2](#p92--sargability-drill) | Sargability drill | 🟢 Easy | 10 min |
| [P9.3](#p93--parameter-sniffing) | Parameter sniffing | 🔴 Hard | 20 min |
| [P9.4](#p94--pagination) | Pagination | 🟡 Medium | 15 min |

### P9.1 — Index from a query

🔴 Hard · 25 min

**Task:** The tee sheet query returns tee times for one course and one UTC day with at least
`@minSpots` free spots (`Capacity - BookedPlayers`), ordered by `StartUtc`. Run it for course 1,
2026-11-10, `@minSpots = 2`. Record reads and the plan, design the index (key order + `INCLUDE`),
and measure again. Also decide which FK columns (`Bookings.TeeTimeId`, `Bookings.GolferId`,
`Payments.BookingId`, `TeeTimes.CourseId`) need an index.

**Expected output** (the same before and after the index)

| TeeTimeId | StartUtc | FreeSpots |
|---|---|---|
| 1 | 2026-11-10 14:00 | 4 |
| 2 | 2026-11-10 14:10 | 3 |
| 3 | 2026-11-10 14:20 | 2 |
| 6 | 2026-11-10 14:50 | 4 |
| 7 | 2026-11-10 15:00 | 2 |
| 8 | 2026-11-10 15:10 | 4 |

If you see tee time 5 and not 6, you skipped P7.2 (the counters are still wrong).

**Expected plan:** before, a **Clustered Index Scan** on `TeeTimes` + a Sort. After
`(CourseId, StartUtc) INCLUDE (Capacity, BookedPlayers, Price)`: an **Index Seek**, no Sort, no Key Lookup.

[Answer](source-other-interviews/forte-live-coding-mock-plan.md#124-hints-for-the-tricky-exercises)

### P9.2 — Sargability drill

🟢 Easy · 10 min

**Task:** Rewrite each predicate so it can seek, and check that the result doesn't change.
*(`YEAR()`, leading `%` and `N''` literals are already in [mock-interview-questions.md §8](mock-interview-questions.md#8-query-optimization).)*

| Original | Expected result |
|---|---|
| `SELECT COUNT(*) FROM TeeTimes WHERE CAST(StartUtc AS DATE) = '2026-11-10'` | `11` |
| `SELECT BookingId FROM Bookings WHERE ISNULL(HoldExpiresUtc, '9999-12-31') < SYSUTCDATETIME()` | booking `7` (the only hold already past its expiry) |

[Source](source-other-interviews/forte-live-coding-mock-plan.md#d6--sargability-15-min)

### P9.3 — Parameter sniffing

🔴 Hard · 20 min

**Task:** Create `usp_BookingsByCourse @CourseId` that returns bookings joined to tee times for one
course. Add an index on `TeeTimes(CourseId)`. Insert 50,000 extra tee times for a new course 3 (use a
tally CTE), so course 3 is huge and courses 1 and 2 are tiny. Run the procedure for course 2 first,
then for course 3, and compare the plans. Then try `OPTION (RECOMPILE)`, `OPTIMIZE FOR`, and
splitting into two procedures.

**Expected behaviour**

- On a fresh load, course 2 returns **3** rows (bookings 8, 9 and 11).
- Run course 2 first, then course 3: course 3 **reuses** the small-course plan (Index Seek + Nested
  Loops + Key Lookup). The plan's estimated rows are tiny but the actual rows are ~50,000, and the
  logical reads are much higher than a scan would need.
- Clear the plan (`sp_recompile 'usp_BookingsByCourse'`) and run course 3 first: now course 2
  reuses a scan plan.
- With `OPTION (RECOMPILE)`, each call gets its own plan (cost: a compile every call).

[Source](source-other-interviews/forte-live-coding-mock-plan.md#d7--parameter-sniffing-20-min)

### P9.4 — Pagination

🟡 Medium · 15 min

**Task:** List all tee times ordered by `StartUtc, TeeTimeId`, **5 per page**. Get page 2 with
`OFFSET/FETCH`, then get the same page with **keyset** pagination, starting after the last row of
page 1 (`@s = '2026-11-10 14:10'`, `@id = 2`). Why must `TeeTimeId` be in the `ORDER BY`?

**Expected output** (both versions return the same page 2)

| TeeTimeId | StartUtc |
|---|---|
| 3 | 2026-11-10 14:20 |
| 4 | 2026-11-10 14:30 |
| 5 | 2026-11-10 14:40 |
| 6 | 2026-11-10 14:50 |
| 7 | 2026-11-10 15:00 |

Page 1 is tee times 9, 10, 11, 1, 2; page 3 is tee times 8, 12.

[Source](source-other-interviews/forte-live-coding-mock-plan.md#d10--pagination-15-min)

---

## 10. Time Zones, Bulk Loads and SQL Code Review

| # | Scenario | Difficulty | Suggested time |
|---|---|---|---|
| [P10.1](#p101--utilization-per-local-day) | Utilization per local day | 🟡 Medium | 15 min |
| [P10.2](#p102--bulk-insert) | Bulk insert | 🟡 Medium | 15 min |
| [P10.3](#p103--review-a-sql-pull-request) | Review a SQL pull request | 🔴 Hard | 10 min |

### P10.1 — Utilization per local day

🟡 Medium · 15 min

**Task:** Per course and per **course-local** date, show tee times offered, real players booked
(non-cancelled), total capacity and utilization % (booked / capacity, 2 decimals), ordered by
utilization desc. Use the original data (or the data after P7.2; it gives the same result).

Two traps:
- `AT TIME ZONE` only accepts **Windows** names (`'Central Standard Time'`, `'GMT Standard Time'`),
  but `Courses.TimeZoneId` holds IANA names. Build a small mapping (for example a `VALUES` list).
- `SELECT SUM(BookedPlayers) / SUM(Capacity) FROM TeeTimes` returns `0` (integer division).

**Expected output**

| CourseId | Name | LocalDate | Offered | Booked | Capacity | UtilizationPct |
|---|---|---|---|---|---|---|
| 2 | Hollow Moor    | 2026-11-10 | 3 | 9  | 12 | 75.00 |
| 1 | Cedar Ridge GC | 2026-11-10 | 8 | 12 | 32 | 37.50 |
| 1 | Cedar Ridge GC | 2026-11-11 | 1 | 1  | 4  | 25.00 |

Check: `SELECT SYSUTCDATETIME() AT TIME ZONE 'UTC' AT TIME ZONE 'America/Chicago'` fails with an
"invalid time zone" error.

[Source](source-other-interviews/forte-live-coding-mock-plan.md#d1--joins--aggregation-15-min--warm-up)

### P10.2 — Bulk insert

🟡 Medium · 15 min

**Task:** Add a new course, then insert its tee times in **one** call: first from a JSON array with
`OPENJSON ... WITH (...)`, then (after deleting them) with a table-valued parameter
(`CREATE TYPE dbo.TeeTimeList AS TABLE (...)`). Then scale it to 500 rows and compare with 500
single-row `INSERT`s.

```sql
INSERT INTO Courses VALUES (3, 'Kingsfield Park', 'Europe/London', 'UK', 'GBP');

DECLARE @json NVARCHAR(MAX) = N'[
  {"id": 101, "startUtc": "2026-11-12T08:00:00", "price": 60.00},
  {"id": 102, "startUtc": "2026-11-12T08:10:00", "price": 60.00},
  {"id": 103, "startUtc": "2026-11-12T08:20:00", "price": 65.00}
]';
```

**Expected output** (`SELECT TeeTimeId, CourseId, StartUtc, Capacity, BookedPlayers, Price FROM TeeTimes WHERE CourseId = 3`)

| TeeTimeId | CourseId | StartUtc | Capacity | BookedPlayers | Price |
|---|---|---|---|---|---|
| 101 | 3 | 2026-11-12 08:00 | 4 | 0 | 60.00 |
| 102 | 3 | 2026-11-12 08:10 | 4 | 0 | 60.00 |
| 103 | 3 | 2026-11-12 08:20 | 4 | 0 | 65.00 |

`Capacity` and `BookedPlayers` come from the column defaults. The TVP version must give the same rows.

[Source](source-other-interviews/forte-live-coding-mock-plan.md#d12--bulk-insert-15-min)

### P10.3 — Review a SQL pull request

🔴 Hard · 10 min

**Task:** A teammate opens this pull request. List every issue with its impact, then rewrite it.
Target: 7+ issues.

```sql
CREATE PROCEDURE GetCourseBookings @CourseName NVARCHAR(100) AS
BEGIN
  DECLARE @sql NVARCHAR(MAX) = 'SELECT * FROM Bookings b, TeeTimes t, Courses c
     WHERE b.TeeTimeId = t.TeeTimeId AND t.CourseId = c.CourseId
     AND c.Name = ''' + @CourseName + ''' AND YEAR(t.StartUtc) = YEAR(GETDATE())
     ORDER BY t.StartUtc'
  EXEC(@sql)
END
```

**Expected output**

- Your list covers: SQL injection, comma joins, `SELECT *`, non-sargable `YEAR()`, `GETDATE()` vs
  UTC data, no paging, filtering by name instead of id, no `SET NOCOUNT ON`, no `dbo.` prefix,
  cancelled bookings not filtered.
- Proof of the injection: `EXEC GetCourseBookings N'x'' OR 1=1 --';` returns bookings for **every**
  course. Your rewrite must return **0 rows** for that input.
- On a **fresh load** of the sample data, your rewrite run with `N'Cedar Ridge GC'` for 2026 returns
  the 7 non-cancelled bookings of course 1 (bookings 1, 2, 3, 4, 5, 7, 10), without booking 6.

[Answer](source-other-interviews/forte-live-coding-mock-plan.md#123-e3--sql-pr-issues)

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
