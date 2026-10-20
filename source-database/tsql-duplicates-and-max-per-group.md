# T-SQL Interview Prep: Find Duplicates and Identify Maximum Values Within Groups

A practice log of 5 mock interview exercises. Each follows the same pattern:

> Find the groups with **more than one row** (duplicates), then for each group return the **count**, the **maximum value**, and the **date/row where that maximum occurred**.

Two approaches are practiced for every exercise:

1. **GROUP BY + HAVING + correlated subquery**
2. **CTE + window functions (`PARTITION BY`, `ROW_NUMBER()`)**

---

## Table of Contents

1. [Core Concepts](#1-core-concepts)
2. [The Two Solution Patterns (Templates)](#2-the-two-solution-patterns-templates)
3. [Exercise 1: Sales (Customers)](#3-exercise-1-sales-customers)
4. [Exercise 2: Employee Logins](#4-exercise-2-employee-logins)
5. [Exercise 3: Product Orders](#5-exercise-3-product-orders)
6. [Exercise 4: Website Page Views](#6-exercise-4-website-page-views)
7. [Exercise 5: Package Deliveries](#7-exercise-5-package-deliveries)
8. [Common Mistakes Cheat Sheet](#8-common-mistakes-cheat-sheet)
9. [Edge Cases: Ties and Determinism](#9-edge-cases-ties-and-determinism)
10. [Approach Comparison](#10-approach-comparison)
11. [Follow-up Questions an Interviewer Might Ask](#11-follow-up-questions-an-interviewer-might-ask)
12. [Quick Syntax Reference](#12-quick-syntax-reference)

---

## 1. Core Concepts

### WHERE vs HAVING
- `WHERE` filters **individual rows**, before grouping.
- `HAVING` filters **groups**, after `GROUP BY`. It is `HAVING`, never `HAVING BY`.

### Aggregate functions work independently
`MAX(Amount)` and `MAX(TransactionDate)` do not "talk to each other". `MAX(TransactionDate)` returns the latest date, **not** the date on which the max amount happened. To get the date that belongs to the max amount you need either:
- a correlated subquery with `TOP 1 ... ORDER BY <value> DESC`, or
- `ROW_NUMBER() OVER (PARTITION BY ... ORDER BY <value> DESC)` and keep `RowNum = 1`.

### What PARTITION BY does
`PARTITION BY` creates separate "mini-tables" (one per group) and applies the window function inside each one, **without collapsing rows** (unlike `GROUP BY`).

```
[John Smith partition]        [Jane Doe partition]
Amount | RowNum               Amount | RowNum
400    | 1  <- highest        225    | 1  <- highest
250    | 2                    150    | 2
175    | 3
```

### Window function syntax
```sql
FUNCTION() OVER (PARTITION BY group_col ORDER BY order_col)
```
Examples:
```sql
COUNT(*)      OVER (PARTITION BY CustomerID)
MAX(Amount)   OVER (PARTITION BY CustomerID)
ROW_NUMBER()  OVER (PARTITION BY CustomerID ORDER BY Amount DESC)
```

### Scalar subquery rule
A subquery in the `SELECT` list must return **exactly one value**. Use `TOP 1` (with `ORDER BY`) or an aggregate.

---

## 2. The Two Solution Patterns (Templates)

### Pattern A: GROUP BY + correlated subquery
```sql
SELECT
    g.GroupID,
    g.GroupName,
    COUNT(*)        AS Total,
    MAX(g.Value)    AS MaxValue,
    (
        SELECT TOP 1 s.TheDate
        FROM   SameTable s
        WHERE  s.GroupID = g.GroupID      -- correlate to outer row
        ORDER BY s.Value DESC             -- biggest first
    ) AS MaxValueDate
FROM SameTable g
GROUP BY g.GroupID, g.GroupName
HAVING COUNT(*) > 1;                      -- duplicates only
```

### Pattern B: CTE + window functions
```sql
WITH cteResult AS (
    SELECT
        GroupID,
        GroupName,
        TheDate,
        COUNT(*)     OVER (PARTITION BY GroupID)                         AS Total,
        MAX(Value)   OVER (PARTITION BY GroupID)                         AS MaxValue,
        ROW_NUMBER() OVER (PARTITION BY GroupID ORDER BY Value DESC)     AS RowNum
    FROM SameTable
)
SELECT
    GroupID,
    GroupName,
    Total,
    MaxValue,
    TheDate AS MaxValueDate
FROM cteResult
WHERE RowNum = 1      -- the row holding the max
  AND Total  > 1;     -- duplicates only
```

---

## 3. Exercise 1: Sales (Customers)

### Scenario
A `Sales` table tracks transactions. Find customers who made more than one purchase, and for each show the total number of transactions, the maximum transaction amount, and the date of that maximum transaction.

### Schema and sample data
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

### Expected output
| CustomerID | CustomerName | TotalTransactions | MaxAmount | MaxTransactionDate |
|---|---|---|---|---|
| 101 | John Smith | 3 | 400.00 | 2024-01-20 |
| 102 | Jane Doe   | 2 | 225.00 | 2024-02-05 |
| 103 | Bob Wilson | 2 | 450.00 | 2024-02-10 |

Alice Brown is excluded (only 1 transaction).

### Your learning path (step by step)

**Step 1: find duplicates.** Your attempt:
```sql
SELECT CustomerName, Count(*) as TotalPurchase
FROM Sales
GROUP BY CustomerName
HAVING BY Count(*) > 1      -- ERROR: "HAVING BY" is not valid
```
Fix: `HAVING Count(*) > 1`.

**Step 2: add the max amount.** Your attempt (correct, only missing an alias):
```sql
SELECT CustomerName,
       Count(*) as Totaltransactions,
       MAX(Amount)                      -- add: AS MaxAmount
FROM Sales
GROUP BY CustomerName
HAVING Count(*) > 1
```

**Step 3: add the date of the max (the tricky part).** Your attempt:
```sql
MAX(TransactionDate)   -- WRONG: this is the latest date, NOT the date of the max amount
```
For John Smith: max amount is 400 (Jan 20), but `MAX(TransactionDate)` returns Feb 1, a different transaction.

**Step 4: correlated subquery.** Your version had a missing comma after `MAX(s.Amount) AS MaxAmount`, and used `Count(s.*)` which is invalid (use `Count(*)`).

### Final solution A: GROUP BY + correlated subquery
```sql
SELECT
    s.CustomerName,
    COUNT(*)        AS TotalTransactions,
    MAX(s.Amount)   AS MaxAmount,
    (
        SELECT TOP 1 s2.TransactionDate
        FROM   Sales s2
        WHERE  s2.CustomerName = s.CustomerName
        ORDER BY s2.Amount DESC
    ) AS MaxTransactionDate
FROM Sales AS s
GROUP BY s.CustomerName
HAVING COUNT(*) > 1;
```
Best practice: group by `CustomerID` as well as `CustomerName`, in case two different people share a name.

### Final solution B: CTE + PARTITION BY
```sql
WITH RankedSales AS (
    SELECT
        CustomerName,
        Amount,
        TransactionDate,
        ROW_NUMBER() OVER (PARTITION BY CustomerName ORDER BY Amount DESC) AS RowNum,
        COUNT(*)     OVER (PARTITION BY CustomerName)                       AS TotalTransactions
    FROM Sales
)
SELECT
    CustomerName,
    TotalTransactions,
    Amount          AS MaxAmount,
    TransactionDate AS MaxTransactionDate
FROM RankedSales
WHERE RowNum = 1
  AND TotalTransactions > 1
ORDER BY CustomerName;
```

### Intermediate CTE result (for understanding)
| CustomerName | Amount | TransactionDate | RowNum | TotalTransactions |
|---|---|---|---|---|
| Bob Wilson  | 450.00 | 2024-02-10 | **1** | 2 |
| Bob Wilson  | 300.00 | 2024-01-18 | 2 | 2 |
| Jane Doe    | 225.00 | 2024-02-05 | **1** | 2 |
| Jane Doe    | 150.00 | 2024-01-16 | 2 | 2 |
| John Smith  | 400.00 | 2024-01-20 | **1** | 3 |
| John Smith  | 250.00 | 2024-01-15 | 2 | 3 |
| John Smith  | 175.00 | 2024-02-01 | 3 | 3 |
| Alice Brown | 500.00 | 2024-01-22 | 1 | **1** (filtered out) |

### Earlier wrong attempts (do not repeat)
```sql
SELECT SUM(DISTINCT(Transaction)) FROM Sales      -- wrong column name; sums unique values over the whole table
SELECT MAX(Transaction) FROM Sales                -- wrong column name; overall max, not per customer
SELECT TransactionDate, MAX(Transaction)
FROM Sales GROUP BY TransactionDate               -- groups by date instead of customer
```
Lessons: check column names against the schema (`Amount`, not `Transaction`), and group by the entity the question is about (customer), not by date.

### Alternative: multiple CTEs (more readable)
```sql
WITH CustomerCounts AS (
    SELECT CustomerID, CustomerName, COUNT(*) AS TotalTransactions
    FROM Sales
    GROUP BY CustomerID, CustomerName
    HAVING COUNT(*) > 1
),
MaxTransactions AS (
    SELECT CustomerID, MAX(Amount) AS MaxAmount
    FROM Sales
    GROUP BY CustomerID
)
SELECT cc.CustomerID, cc.CustomerName, cc.TotalTransactions,
       mt.MaxAmount, s.TransactionDate AS MaxTransactionDate
FROM CustomerCounts cc
JOIN MaxTransactions mt ON cc.CustomerID = mt.CustomerID
JOIN Sales s ON s.CustomerID = cc.CustomerID AND s.Amount = mt.MaxAmount;
```
Note: if two transactions tie for the max amount, this version returns both rows.

### Alternative: my own version (explicit `MAX` window)

Same idea as solution B, but `MaxAmount` comes from its own `MAX(Amount) OVER (...)` instead of
reusing `Amount` from the `RowNum = 1` row. It also uses `CustomerID` in every partition and names
the final columns.

```sql
WITH cte_overview AS
(
    SELECT  CustomerID,
            CustomerName,
            TransactionDate,
            COUNT(*)    OVER (PARTITION BY CustomerID) AS TotalTransactions,
            MAX(Amount) OVER (PARTITION BY CustomerID) AS MaxAmount,
            ROW_NUMBER() OVER (PARTITION BY CustomerID ORDER BY Amount DESC) AS RowNum
    FROM dbo.Sales
)
SELECT  CustomerID,
        CustomerName,
        TotalTransactions,
        MaxAmount,
        TransactionDate AS MaxTransactionDate
FROM cte_overview
WHERE TotalTransactions > 1
  AND RowNum = 1
ORDER BY CustomerID;
```

**Pros**
- **Self-explaining:** `MaxAmount` says what it is. You don't need to remember that `Amount`
  on the `RowNum = 1` row happens to be the max, so it's harder to break by accident later
  (for example if someone changes the `ROW_NUMBER` order).
- **Cheap:** `COUNT`, `MAX` and `ROW_NUMBER` all use `PARTITION BY CustomerID`, so SQL Server
  sorts by `CustomerID` **once** and computes all three in the same pass. The extra `MAX` only
  adds a little CPU per row.
- **Correct key:** partitioning by `CustomerID` (not `CustomerName`) keeps two customers with the
  same name apart.
- **Matches the expected output:** explicit column list, `MaxTransactionDate` alias, `ORDER BY`.

**Cons**
- **One more window aggregate** than solution B, which reuses `Amount AS MaxAmount`. It doesn't
  matter on small tables; on millions of rows it is still minor compared to the sort.
- **Window Spool in row mode:** `COUNT(*) OVER` and `MAX() OVER` (no `ORDER BY` inside `OVER`)
  write rows to a worktable in tempdb (you can see `Worktable` reads in `SET STATISTICS IO`).
  On SQL Server 2019+ with large data, batch mode's Window Aggregate operator avoids this.
- **Ties:** `ROW_NUMBER` picks one row when two transactions share the max amount, and which one
  can change between runs. Add a tie-breaker (`ORDER BY Amount DESC, TransactionDate DESC`) for a
  stable result, or use `RANK()` to return every tied row.
- **No early filter:** every row is numbered and counted before `TotalTransactions > 1` removes
  single-purchase customers. A `GROUP BY ... HAVING` step first can be cheaper when most customers
  have only one purchase.

**Performance tip:** an index that already returns rows in partition order removes the sort:

```sql
CREATE INDEX IX_Sales_Customer_Amount
ON dbo.Sales (CustomerID, Amount DESC)
INCLUDE (CustomerName, TransactionDate);
```

Keep every `OVER (...)` on the same `PARTITION BY` / `ORDER BY`. Mixing `CustomerID` and
`CustomerName` partitions forces a **second sort**, which costs far more than the extra `MAX`.

---

## 4. Exercise 2: Employee Logins

### Scenario
An `EmployeeLogins` table tracks logins. Return employees who logged in more than once, with total logins, the time of their **first** login, the time of their **last** login, and the date of their first login. "First" and "last" are in time order (date, then time).

### Schema and sample data
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

### Expected output
| EmployeeID | EmployeeName | Department | TotalLogins | FirstLoginTime | LastLoginTime | FirstLoginDate |
|---|---|---|---|---|---|---|
| 501 | Sarah Chen   | IT    | 3 | 08:30:00 | 16:00:00 | 2024-12-15 |
| 502 | Mike Johnson | Sales | 2 | 09:00:00 | 08:15:00 | 2024-12-15 |
| 503 | Lisa Wong    | HR    | 2 | 08:45:00 | 10:00:00 | 2024-12-15 |

> **The trap in this exercise (Mike Johnson):** his first login is 2024-12-15 at **09:00** and his last
> login is 2024-12-16 at **08:15**. So `LastLoginTime` is *earlier in the day* than `FirstLoginTime`.
>
> `MIN(LoginTime)` / `MAX(LoginTime)` **don't** give that. They only compare the time of day and
> ignore the date, so they return Mike `08:15 / 09:00`: the wrong way round. To get the time of
> the first / last login you must order by **date, then time**:
> - `TOP (1) ... ORDER BY LoginDate, LoginTime` (ASC for first, DESC for last), or
> - `FIRST_VALUE(LoginTime) OVER (... ORDER BY LoginDate, LoginTime)` (ASC for first, DESC for last).
>
> Sarah Chen and Lisa Wong give the same answer both ways, so only Mike shows the bug. In an
> interview, say this out loud: "Do you mean the earliest time of day, or the time of the first login?"

### Your GROUP BY attempt and the fixes
```sql
(SELECT top(1) LoginDate
 FROM EmployeeLogins b
 WHERE b.EmployeeID = e.b.EmployeeID    -- ERROR 1: "e.b.EmployeeID" does not exist, use e.EmployeeID
 ORDER BY ASC)                           -- ERROR 2: ORDER BY needs a column name
```

### Final solution A: GROUP BY + correlated subquery
```sql
SELECT
    e.EmployeeID,
    e.EmployeeName,
    e.Department,
    COUNT(*)         AS TotalLogins,
    (
        SELECT TOP (1) f.LoginTime
        FROM   EmployeeLogins f
        WHERE  f.EmployeeID = e.EmployeeID
        ORDER BY f.LoginDate ASC, f.LoginTime ASC      -- oldest login first
    )                AS FirstLoginTime,
    (
        SELECT TOP (1) l.LoginTime
        FROM   EmployeeLogins l
        WHERE  l.EmployeeID = e.EmployeeID
        ORDER BY l.LoginDate DESC, l.LoginTime DESC    -- newest login first
    )                AS LastLoginTime,
    MIN(e.LoginDate) AS FirstLoginDate                 -- the earliest date is just MIN
FROM EmployeeLogins e
GROUP BY e.EmployeeID, e.EmployeeName, e.Department
HAVING COUNT(*) > 1
ORDER BY e.EmployeeID;
```
`FirstLoginDate` doesn't need a subquery: `MIN(LoginDate)` is the date of the first login.

### Lessons for the window-function version
- Plain aggregates (`MIN()`) need `GROUP BY`; window versions (`MIN() OVER (...)`) don't. Don't mix them in the same `SELECT`.
- Any column used in the outer query (`LoginDate`, `LoginTime`) must be selected inside the CTE.
- `PARTITION BY EmployeeID` is enough; you don't need to partition by every column.

### Final solution B: CTE + window functions
```sql
WITH cteLogins AS (
    SELECT
        EmployeeID,
        EmployeeName,
        Department,
        LoginDate,
        COUNT(*) OVER (PARTITION BY EmployeeID) AS TotalLogins,
        FIRST_VALUE(LoginTime) OVER (PARTITION BY EmployeeID ORDER BY LoginDate ASC,  LoginTime ASC)  AS FirstLoginTime,
        FIRST_VALUE(LoginTime) OVER (PARTITION BY EmployeeID ORDER BY LoginDate DESC, LoginTime DESC) AS LastLoginTime,
        ROW_NUMBER()           OVER (PARTITION BY EmployeeID ORDER BY LoginDate ASC,  LoginTime ASC)  AS RowNum
    FROM EmployeeLogins
)
SELECT
    EmployeeID,
    EmployeeName,
    Department,
    TotalLogins,
    FirstLoginTime,
    LastLoginTime,
    LoginDate AS FirstLoginDate
FROM cteLogins
WHERE RowNum = 1          -- the first login row
  AND TotalLogins > 1     -- duplicates only
ORDER BY EmployeeID;
```
Notes:
- **Last login = first login in reverse order.** `FIRST_VALUE` with `DESC` gives the last login.
  Avoid `LAST_VALUE`: with an `ORDER BY`, its default frame stops at the current row, so it needs
  `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING` to work.
- **Don't put `ORDER BY` inside `MIN() OVER` / `MAX() OVER`** here. It turns them into running
  values (only the rows up to the current one), which is a different bug.
- `LoginTime ASC` is the tie-breaker for `RowNum`, because Sarah Chen has 3 logins on the same date.

Both solutions were run on SQL Server and return exactly the expected output above.

---

## 5. Exercise 3: Product Orders

### Scenario
An `Orders` table tracks purchases. Find products ordered more than once; show total orders, the highest quantity in a single order, and the order date of that highest quantity.

### Schema and sample data
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

### Expected output
| ProductID | ProductName | TotalOrders | MaxQuantity | MaxQuantityOrderDate |
|---|---|---|---|---|
| 201 | Laptop   | 3 | 12 | 2024-11-15 |
| 202 | Mouse    | 2 | 35 | 2024-11-22 |
| 203 | Keyboard | 2 | 15 | 2024-11-25 |

### Your first attempt (window syntax was backwards)
```sql
PARTITION BY (OrderID, ProductID) OVER (MAX(Quantity)) AS 'MaxQuantity'    -- WRONG ORDER
PARTITION BY (Quantity) OVER (MAX(OrderDate)) 'MaxQuantityOrderDate'       -- WRONG
```
Problems:
1. Syntax is inverted. Correct: `MAX(Quantity) OVER (PARTITION BY ProductID)`.
2. Missing commas (after `OrderID`, and after `ProductName` in the final SELECT).
3. Wrong partition: `OrderID` is unique per row, so each partition has one row. Partition by `ProductID`.
4. You need `ROW_NUMBER()` to identify which row holds the max.
5. Do not combine window columns with an outer `GROUP BY` the way the attempt did; filter with `WHERE RowNum = 1` instead.

### Final solution B: CTE + window functions
```sql
WITH cteResult AS (
    SELECT
        OrderID,
        ProductID,
        ProductName,
        Quantity,
        OrderDate,
        COUNT(*)     OVER (PARTITION BY ProductID)                          AS TotalOrders,
        MAX(Quantity) OVER (PARTITION BY ProductID)                         AS MaxQuantity,
        ROW_NUMBER() OVER (PARTITION BY ProductID ORDER BY Quantity DESC)   AS RowNum
    FROM Orders
)
SELECT
    ProductID,
    ProductName,
    TotalOrders,
    MaxQuantity,
    OrderDate AS MaxQuantityOrderDate
FROM cteResult
WHERE RowNum = 1
  AND TotalOrders > 1
ORDER BY ProductID;
```

### Your GROUP BY attempt and the fix
Your subquery was missing `TOP 1`:
```sql
(SELECT OrderDate FROM Orders s
 WHERE s.ProductID = o.ProductID
 ORDER BY Quantity DESC)       -- returns several rows -> error: "Subquery returned more than 1 value"
```

### Final solution A: GROUP BY + correlated subquery
```sql
SELECT
    ProductID,
    ProductName,
    COUNT(*)       AS TotalOrders,
    MAX(Quantity)  AS MaxQuantity,
    (
        SELECT TOP 1 s.OrderDate
        FROM Orders s
        WHERE s.ProductID = o.ProductID
        ORDER BY s.Quantity DESC
    ) AS MaxQuantityOrderDate
FROM Orders o
GROUP BY ProductID, ProductName
HAVING COUNT(*) > 1;
```

---

## 6. Exercise 4: Website Page Views

### Scenario
A `PageViews` table tracks visits. Find returning users (more than one visit), with total visits, longest session duration (minutes), and the date of that longest session.

### Schema and sample data
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

### Expected output
| UserID | UserName | TotalVisits | LongestSession | LongestSessionDate |
|---|---|---|---|---|
| 301 | Emily Davis  | 3 | 120 | 2024-12-03 |
| 302 | James Wilson | 2 | 25  | 2024-12-06 |
| 303 | Maria Garcia | 2 | 75  | 2024-12-07 |

### Your attempt and the 3 mistakes
```sql
WITH cteResult AS (
    SELECT UserID, UserName,
           COUNT(*) OVER (PARTITION BY UserID) as 'TotalVisits',
           MAX(SessionDuration) OVER (PARTITION BY UserID) as 'LongestSession',
           ROW_NUMBER() OVER (PARTITION BY SessionDuration           -- MISTAKE 1
                              ORDER BY SessionDuration DESC) as ROW_NUMBER_VISI_DATE
    FROM PageViews
)
SELECT UserID, UserName,
       'TotalVisits',                                                -- MISTAKE 2
       'LongestSession',                                             -- MISTAKE 2
       VisitDate as 'LongestSessionDate'                             -- MISTAKE 3
FROM cteResult
WHERE TotalVisits > 1 and ROW_NUMBER_VISI_DATE = 1
```

1. **Wrong PARTITION BY.** `PARTITION BY SessionDuration` groups all rows with the same duration, so every row ends up with `RowNum = 1`. It must be `PARTITION BY UserID`, then `ORDER BY SessionDuration DESC`.
2. **Quotes around column names in the final SELECT.** `'TotalVisits'` is a **string literal**, so the query returns the text "TotalVisits" on every row. Use `TotalVisits` or `[TotalVisits]`.
3. **Column not in the CTE.** `VisitDate` was not selected inside the CTE, so the outer query cannot see it.

Visual of mistake 1:

| UserID | SessionDuration | PARTITION BY SessionDuration (wrong) | RowNum |
|---|---|---|---|
| 301 | 45  | group of 45s  | 1 |
| 302 | 15  | group of 15s  | 1 |
| 301 | 120 | group of 120s | 1 |

Every user gets `RowNum = 1`, so the filter cannot isolate the max.

### Final solution B: CTE + window functions
```sql
WITH cteResult AS (
    SELECT
        UserID,
        UserName,
        VisitDate,
        SessionDuration,
        COUNT(*)              OVER (PARTITION BY UserID)                              AS TotalVisits,
        MAX(SessionDuration)  OVER (PARTITION BY UserID)                              AS LongestSession,
        ROW_NUMBER()          OVER (PARTITION BY UserID ORDER BY SessionDuration DESC) AS RowNum
    FROM PageViews
)
SELECT
    UserID,
    UserName,
    TotalVisits,
    LongestSession,
    VisitDate AS LongestSessionDate
FROM cteResult
WHERE RowNum = 1
  AND TotalVisits > 1;
```

### Reference solution A: GROUP BY + correlated subquery
```sql
SELECT
    UserID,
    UserName,
    COUNT(*)              AS TotalVisits,
    MAX(SessionDuration)  AS LongestSession,
    (
        SELECT TOP 1 s.VisitDate
        FROM PageViews s
        WHERE s.UserID = p.UserID
        ORDER BY s.SessionDuration DESC
    ) AS LongestSessionDate
FROM PageViews p
GROUP BY UserID, UserName
HAVING COUNT(*) > 1;
```

---

## 7. Exercise 5: Package Deliveries

### Scenario
A `Deliveries` table tracks shipments. Find customers with more than one delivery, with total deliveries, heaviest package weight (kg), and the delivery date of that heaviest package.

### Schema and sample data
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

### Expected output
| CustomerID | CustomerName | TotalDeliveries | HeaviestPackage | HeaviestPackageDate |
|---|---|---|---|---|
| 401 | Chris Brown | 3 | 12.75 | 2024-12-12 |
| 402 | Anna Taylor | 2 | 9.50  | 2024-12-16 |
| 403 | Kevin Zhang | 2 | 15.30 | 2024-12-18 |

### Your solution B: CTE + window functions (correct on the first try)
```sql
WITH cteResult AS (
    SELECT
        DeliveryID,
        CustomerID,
        CustomerName,
        DeliveryDate,
        COUNT(*)           OVER (PARTITION BY CustomerID)                               AS TotalDeliveries,
        MAX(PackageWeight) OVER (PARTITION BY CustomerID)                               AS HeaviestPackage,
        ROW_NUMBER()       OVER (PARTITION BY CustomerID ORDER BY PackageWeight DESC)   AS ROW_NUMBER_HeaviestPackageDate
    FROM Deliveries
)
SELECT
    CustomerID,
    CustomerName,
    TotalDeliveries,
    HeaviestPackage,
    DeliveryDate AS HeaviestPackageDate
FROM cteResult
WHERE TotalDeliveries > 1
  AND ROW_NUMBER_HeaviestPackageDate = 1;
```

### Your solution A: GROUP BY + correlated subquery (correct)
```sql
SELECT
    CustomerID,
    CustomerName,
    COUNT(*)           AS TotalDeliveries,
    MAX(PackageWeight) AS HeaviestPackage,
    (
        SELECT TOP(1) DeliveryDate
        FROM Deliveries s
        WHERE s.CustomerID = d.CustomerID
        ORDER BY PackageWeight DESC
    ) AS HeaviestPackageDate
FROM Deliveries d
GROUP BY CustomerID, CustomerName
HAVING COUNT(*) > 1;
```
Style tip: write `ORDER BY s.PackageWeight DESC` (prefix with the alias) for clarity. Inside the subquery an unqualified column resolves to the inner table, so it works as-is.

---

## 8. Common Mistakes Cheat Sheet

| # | Mistake | Why it is wrong | Fix |
|---|---|---|---|
| 1 | `HAVING BY COUNT(*) > 1` | "BY" is not part of the syntax | `HAVING COUNT(*) > 1` |
| 2 | `Count(s.*)` | `alias.*` is not valid inside COUNT | `COUNT(*)` or `COUNT(column)` |
| 3 | Missing comma between SELECT columns | Syntax error | Put a comma after every column but the last |
| 4 | Trailing comma before `FROM` | Syntax error | Remove the last comma |
| 5 | Using a non-existent column (`Transaction` instead of `Amount`) | Invalid column name | Re-read the schema before writing |
| 6 | `MAX(DateCol)` to get the date of the max amount | Aggregates are independent; this returns the latest date | Correlated subquery with `TOP 1 ... ORDER BY value DESC`, or `ROW_NUMBER()` |
| 7 | Subquery in SELECT without `TOP 1` | Scalar subquery returns more than one row | Add `TOP 1` (with `ORDER BY`) or use an aggregate |
| 8 | `e.b.EmployeeID` | Two aliases joined with a dot do not exist | `e.EmployeeID` and `b.EmployeeID` |
| 9 | `ORDER BY ASC` with no column | ORDER BY needs a column | `ORDER BY LoginDate ASC` |
| 10 | `PARTITION BY (...) OVER (MAX(...))` | Syntax is backwards | `MAX(...) OVER (PARTITION BY ...)` |
| 11 | Partitioning by a unique column (`OrderID`) or by the value being ranked (`SessionDuration`) | Each partition has one row or the wrong grouping, so every row gets `RowNum = 1` | Partition by the group key (`ProductID`, `UserID`, `CustomerID`) |
| 12 | Mixing plain `MIN()/MAX()` with window functions without `GROUP BY` | Plain aggregates require grouping | Use `MIN() OVER (...)` / `MAX() OVER (...)` |
| 13 | Quotes around column names in SELECT (`'TotalVisits'`) | Creates a string literal, not a column reference | No quotes, or `[TotalVisits]` |
| 14 | Column not selected inside the CTE but used in the outer query | The outer query only sees what the CTE exposes | Add the column to the CTE |
| 15 | Grouping only by name | Two customers may share a name | Group by the ID as well (`GROUP BY CustomerID, CustomerName`) |
| 16 | Filtering on a window function in the same SELECT's `WHERE` | Window functions cannot be used in `WHERE` | Compute them in a CTE/subquery and filter in the outer query |
| 17 | Window columns in a CTE then an outer `GROUP BY` | Unnecessary; rows are already one-per-group after `RowNum = 1` | Filter with `WHERE RowNum = 1` |

---

## 9. Edge Cases: Ties and Determinism

**What if two rows tie for the max?**
- `ROW_NUMBER()` always picks exactly one row, but which one is arbitrary unless you add a tie-breaker:
  ```sql
  ROW_NUMBER() OVER (PARTITION BY CustomerID ORDER BY Amount DESC, TransactionDate DESC)
  ```
- `RANK()` or `DENSE_RANK()` with `= 1` returns **all** tied rows.
- `TOP 1` in a correlated subquery is also arbitrary on ties; add a second `ORDER BY` column.
- The "join back on `Amount = MaxAmount`" pattern returns all tied rows.

Decide with the interviewer which behavior is wanted: one row per group, or all tied rows.

---

## 10. Approach Comparison

| | GROUP BY + correlated subquery | CTE + window functions |
|---|---|---|
| Readability | Easy, straightforward | Slightly more syntax to learn |
| Performance on large tables | The subquery runs per group; can be slower | Usually a single pass; scales better |
| Getting extra columns of the max row | One subquery per extra column | All columns available from the ranked row |
| Tie handling | Needs extra `ORDER BY` columns | `ROW_NUMBER` / `RANK` / `DENSE_RANK` give you control |
| Good for | Quick answers, small data, interviews | Larger data, more complex requirements |

**Rule of thumb:** show the simple GROUP BY solution first, then offer the window-function version as the scalable one.

---

## 11. Follow-up Questions an Interviewer Might Ask

1. **What if there are ties for the max?** (See section 9.)
2. **Which approach is more efficient for millions of rows?** Window functions, ideally with an index on `(GroupKey, ValueCol DESC)` to avoid a sort.
3. **How would you get the minimum instead, or both min and max?** Use `MIN`, or `ORDER BY value ASC` for the row number; run two `ROW_NUMBER()` columns (ASC and DESC) for both.
4. **Customers with exactly 2 transactions?** Change `HAVING COUNT(*) > 1` to `HAVING COUNT(*) = 2` (or `TotalX = 2` in the window version).
5. **What is the difference between `ROW_NUMBER`, `RANK`, and `DENSE_RANK`?**
   - `ROW_NUMBER`: 1, 2, 3, 4 (always unique)
   - `RANK`: 1, 1, 3, 4 (ties share a rank, then a gap)
   - `DENSE_RANK`: 1, 1, 2, 3 (ties share a rank, no gap)
6. **Why can't you use a window function in `WHERE`?** Window functions are evaluated after `WHERE`/`GROUP BY`/`HAVING` in the logical processing order, so you must wrap them in a CTE or subquery.
7. **How would you simply list the duplicate rows themselves?** Use `COUNT(*) OVER (PARTITION BY key)` in a CTE and filter `> 1`, or join back to a `GROUP BY ... HAVING COUNT(*) > 1` result.
8. **How would you delete duplicates, keeping one?** CTE with `ROW_NUMBER() OVER (PARTITION BY key ORDER BY id)` then `DELETE FROM cte WHERE RowNum > 1`.

---

## 12. Quick Syntax Reference

### SQL logical processing order
```
FROM -> WHERE -> GROUP BY -> HAVING -> SELECT (incl. window functions) -> ORDER BY -> TOP
```

### Window functions used in this practice
```sql
COUNT(*)        OVER (PARTITION BY key)
MIN(col)        OVER (PARTITION BY key)
MAX(col)        OVER (PARTITION BY key)
ROW_NUMBER()    OVER (PARTITION BY key ORDER BY col DESC)
```

### CTE skeleton
```sql
WITH cteName AS (
    SELECT ...
    FROM ...
)
SELECT ...
FROM cteName
WHERE ...;
```

### Correlated subquery skeleton
```sql
SELECT o.*,
       (SELECT TOP 1 i.SomeCol
        FROM   SameTable i
        WHERE  i.Key = o.Key
        ORDER BY i.SortCol DESC) AS Result
FROM SameTable o;
```

### Checklist before submitting an answer
- [ ] Column names match the schema
- [ ] Commas between all SELECT columns, none trailing
- [ ] `HAVING` (not `HAVING BY`), placed after `GROUP BY`
- [ ] Every non-aggregated SELECT column is in `GROUP BY`
- [ ] Subqueries in SELECT return one row (`TOP 1`)
- [ ] Window syntax is `FUNC() OVER (PARTITION BY ... ORDER BY ...)`
- [ ] `PARTITION BY` is the group key, `ORDER BY` is the value being maximized
- [ ] Columns used in the outer query are selected in the CTE
- [ ] No quotes around column names
- [ ] Ties considered and tie-breaker added if needed
