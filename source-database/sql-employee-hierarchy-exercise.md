# SQL Exercise: Employee Hierarchy with Recursive CTEs

## 📋 Problem Statement

Given an `employees` table with columns `employee_id`, `name`, and `manager_id`, write a query to return the complete employee hierarchy showing each employee's level in the organization.

---

## 📊 Sample Data

### Table Structure

```sql
CREATE TABLE Employees (
    EmployeeID INT PRIMARY KEY,
    Name VARCHAR(100),
    ManagerID INT NULL,
    FOREIGN KEY (ManagerID) REFERENCES Employees(EmployeeID)
);
```

### Sample Records

| employee_id | name   | manager_id |
|-------------|--------|------------|
| 1           | Alice  | NULL       |
| 2           | Bob    | 1          |
| 3           | Carol  | 1          |
| 4           | David  | 2          |
| 5           | Eve    | 2          |
| 6           | Frank  | 3          |
| 7           | Grace  | 4          |

### Insert Statements

```sql
INSERT INTO Employees (EmployeeID, Name, ManagerID) VALUES
(1, 'Alice', NULL),
(2, 'Bob', 1),
(3, 'Carol', 1),
(4, 'David', 2),
(5, 'Eve', 2),
(6, 'Frank', 3),
(7, 'Grace', 4);
```

### Visual Hierarchy

```
Alice (CEO) - Level 0
├── Bob - Level 1
│   ├── David - Level 2
│   │   └── Grace - Level 3
│   └── Eve - Level 2
└── Carol - Level 1
    └── Frank - Level 2
```

---

## 💡 Tips & Hints (Before Looking at the Solution)

### Tip 1: Recognize the Pattern
This is a **self-referencing** (or recursive) table — the table references itself through `manager_id`. You need a way to traverse this relationship multiple times.

### Tip 2: Start from the Root
Who has no manager? That's your starting point (the top of the hierarchy). Look for `manager_id IS NULL`.

### Tip 3: Think Recursively
Ask yourself: "Once I have level 1, how do I find level 2? Once I have level 2, how do I find level 3?" The logic is the same each time — that's a hint about what SQL feature you might need.

### Tip 4: Use Recursive CTEs
Research **CTEs (Common Table Expressions)** — specifically the *recursive* kind. They have two parts:
- A **base case** (anchor member)
- A **recursive case** that references itself

### Tip 5: Track the Level
You'll want a column to show how deep each employee is in the hierarchy. Think about how you'd increment this as you go deeper.

### Tip 6: Build the Path (Optional)
Consider concatenating names or IDs as you traverse, so you can see the full chain like: `Alice → Bob → David → Grace`

---

## ✅ Solution

```sql
WITH EmployeeHierarchy AS (
    -- Base case: start with top-level employees (those without managers)
    SELECT 
        EmployeeID, 
        Name, 
        ManagerID, 
        0 AS Level
    FROM Employees
    WHERE ManagerID IS NULL
    
    UNION ALL
    
    -- Recursive part: join employees with their managers
    SELECT 
        e.EmployeeID, 
        e.Name, 
        e.ManagerID, 
        eh.Level + 1
    FROM Employees e
    JOIN EmployeeHierarchy eh ON e.ManagerID = eh.EmployeeID
)
SELECT 
    EmployeeID,
    Name,
    ManagerID,
    Level
FROM EmployeeHierarchy
ORDER BY Level, EmployeeID;
```

---

## 🔍 Solution Breakdown

| Component | Code | Purpose |
|-----------|------|---------|
| **CTE Declaration** | `WITH EmployeeHierarchy AS (...)` | Creates a named temporary result set that can reference itself |
| **Base Case (Anchor)** | `SELECT ... WHERE ManagerID IS NULL` | Finds the root employee(s) — those with no manager. Sets initial Level to 0 |
| **UNION ALL** | `UNION ALL` | Combines base case with recursive results. Use `UNION ALL` (not `UNION`) for performance |
| **Recursive Member** | `SELECT e.* ... JOIN EmployeeHierarchy eh ON e.ManagerID = eh.EmployeeID` | Finds all employees whose manager is already in the result set |
| **Level Increment** | `eh.Level + 1` | Increases the level for each depth traversed |
| **Final SELECT** | `SELECT ... FROM EmployeeHierarchy ORDER BY Level, EmployeeID` | Outputs the complete hierarchy in order |

### How It Works Step by Step

1. **Iteration 0 (Base Case):** Find Alice (ManagerID IS NULL) → Level 0
2. **Iteration 1:** Find employees where ManagerID = 1 (Alice) → Bob, Carol at Level 1
3. **Iteration 2:** Find employees where ManagerID IN (2, 3) → David, Eve, Frank at Level 2
4. **Iteration 3:** Find employees where ManagerID IN (4, 5, 6) → Grace at Level 3
5. **Iteration 4:** No more employees found → Recursion stops

---

## 📤 Expected Output

| EmployeeID | Name  | ManagerID | Level |
|------------|-------|-----------|-------|
| 1          | Alice | NULL      | 0     |
| 2          | Bob   | 1         | 1     |
| 3          | Carol | 1         | 1     |
| 4          | David | 2         | 2     |
| 5          | Eve   | 2         | 2     |
| 6          | Frank | 3         | 2     |
| 7          | Grace | 4         | 3     |

---

## ⚠️ Common Mistakes

### 1. Using UNION Instead of UNION ALL
```sql
-- ❌ Wrong
UNION
-- ✅ Correct
UNION ALL
```
**Why:** `UNION` removes duplicates (slower). `UNION ALL` keeps all rows and is more efficient for recursive CTEs.

### 2. Wrong Join Condition
```sql
-- ❌ Wrong (reversed logic)
JOIN EmployeeHierarchy eh ON e.EmployeeID = eh.ManagerID

-- ✅ Correct
JOIN EmployeeHierarchy eh ON e.ManagerID = eh.EmployeeID
```
**Why:** You want employees whose `ManagerID` matches an `EmployeeID` already in the result.

### 3. Forgetting the Base Case
```sql
-- ❌ Wrong: No anchor member
WITH EmployeeHierarchy AS (
    SELECT e.EmployeeID, e.Name, e.ManagerID, eh.Level + 1
    FROM Employees e
    JOIN EmployeeHierarchy eh ON e.ManagerID = eh.EmployeeID
)
```
**Why:** Without a starting point, the recursion has nothing to build from.

### 4. Not Handling NULL ManagerID
```sql
-- ❌ Wrong
WHERE ManagerID = NULL

-- ✅ Correct
WHERE ManagerID IS NULL
```
**Why:** In SQL, `NULL = NULL` returns `UNKNOWN`, not `TRUE`. Always use `IS NULL`.

### 5. Infinite Loop Risk
If your data has circular references (A manages B, B manages A), the query will run forever. Add a safeguard:
```sql
-- Add maximum recursion depth (SQL Server)
OPTION (MAXRECURSION 100);

-- Or track visited nodes with a path column
```

### 6. Forgetting Column Aliases in Recursive Part
```sql
-- ❌ Wrong: Missing alias for computed column
SELECT e.EmployeeID, e.Name, e.ManagerID, Level + 1
FROM ...

-- ✅ Correct: Use the CTE alias
SELECT e.EmployeeID, e.Name, e.ManagerID, eh.Level + 1
FROM ... EmployeeHierarchy eh ...
```

---

## 🚀 Advanced Variations

### Variation 1: Add Hierarchy Path
Shows the full chain of management for each employee.

```sql
WITH EmployeeHierarchy AS (
    SELECT 
        EmployeeID, 
        Name, 
        ManagerID, 
        0 AS Level,
        CAST(Name AS VARCHAR(500)) AS Path
    FROM Employees
    WHERE ManagerID IS NULL
    
    UNION ALL
    
    SELECT 
        e.EmployeeID, 
        e.Name, 
        e.ManagerID, 
        eh.Level + 1,
        CAST(eh.Path + ' → ' + e.Name AS VARCHAR(500))
    FROM Employees e
    JOIN EmployeeHierarchy eh ON e.ManagerID = eh.EmployeeID
)
SELECT * FROM EmployeeHierarchy ORDER BY Level, EmployeeID;
```

**Output includes:**
| Name  | Path                          |
|-------|-------------------------------|
| Alice | Alice                         |
| Bob   | Alice → Bob                   |
| Grace | Alice → Bob → David → Grace   |

### Variation 2: Find All Subordinates of a Specific Manager

```sql
WITH Subordinates AS (
    -- Start with the specific manager
    SELECT EmployeeID, Name, ManagerID, 0 AS Level
    FROM Employees
    WHERE EmployeeID = 2  -- Bob's subordinates
    
    UNION ALL
    
    SELECT e.EmployeeID, e.Name, e.ManagerID, s.Level + 1
    FROM Employees e
    JOIN Subordinates s ON e.ManagerID = s.EmployeeID
)
SELECT * FROM Subordinates WHERE Level > 0;  -- Exclude Bob himself
```

### Variation 3: Count Direct and Total Reports

**Idea:** build every (manager, subordinate) pair, at any depth. The anchor is each direct
manager → employee link (depth 1). The recursive part **keeps the same manager** and walks one
level further down. Then count the pairs per manager.

```sql
WITH Reports AS (
    -- Anchor: every direct manager -> employee pair (depth 1)
    SELECT ManagerID, EmployeeID, 1 AS Depth
    FROM Employees
    WHERE ManagerID IS NOT NULL

    UNION ALL

    -- Recursive: keep the same manager, walk one level further down
    SELECT r.ManagerID, e.EmployeeID, r.Depth + 1
    FROM Reports r
    JOIN Employees e ON e.ManagerID = r.EmployeeID
)
SELECT
    m.EmployeeID,
    m.Name,
    COUNT(CASE WHEN r.Depth = 1 THEN 1 END) AS DirectReports,
    COUNT(r.EmployeeID)                     AS TotalReports
FROM Employees m
LEFT JOIN Reports r ON r.ManagerID = m.EmployeeID   -- LEFT JOIN keeps people with no reports (0)
GROUP BY m.EmployeeID, m.Name
ORDER BY m.EmployeeID;
```

**Output:**
| EmployeeID | Name  | DirectReports | TotalReports |
|------------|-------|---------------|--------------|
| 1          | Alice | 2             | 6            |
| 2          | Bob   | 2             | 3            |
| 3          | Carol | 1             | 1            |
| 4          | David | 1             | 1            |
| 5          | Eve   | 0             | 0            |
| 6          | Frank | 0             | 0            |
| 7          | Grace | 0             | 0            |

The `Reports` CTE for Bob (`ManagerID = 2`) holds: David (depth 1), Eve (depth 1), Grace (depth 2)
→ 2 direct, 3 total.

**Alternative: path version.** Build a path of **IDs with delimiters** (`/1/2/4/`), then everyone
whose path starts with a manager's path is in that manager's subtree:

```sql
WITH EmployeeHierarchy AS (
    SELECT EmployeeID, Name, 0 AS Level,
           CAST('/' + CAST(EmployeeID AS VARCHAR(10)) + '/' AS VARCHAR(900)) AS Path
    FROM Employees
    WHERE ManagerID IS NULL

    UNION ALL

    SELECT e.EmployeeID, e.Name, eh.Level + 1,
           CAST(eh.Path + CAST(e.EmployeeID AS VARCHAR(10)) + '/' AS VARCHAR(900))
    FROM Employees e
    JOIN EmployeeHierarchy eh ON e.ManagerID = eh.EmployeeID
)
SELECT
    m.EmployeeID,
    m.Name,
    COUNT(CASE WHEN s.Level = m.Level + 1 THEN 1 END) AS DirectReports,
    COUNT(s.EmployeeID)                               AS TotalReports
FROM EmployeeHierarchy m
LEFT JOIN EmployeeHierarchy s
       ON s.Path LIKE m.Path + '%'          -- s is in m's subtree
      AND s.EmployeeID <> m.EmployeeID      -- but isn't m itself
GROUP BY m.EmployeeID, m.Name, m.Level
ORDER BY m.EmployeeID;
```

Why IDs and delimiters, not names: with a name path, `'Alice → Ann%'` would also match
`'Alice → Anna'`, so Anna would be counted as Ann's report. With IDs and a closing `/`,
`'/1/2/%'` can't match `'/1/23/'`.

| Version | Pros | Cons |
|---|---|---|
| Manager/subordinate pairs | No strings; simple joins; easy to read | Builds one row per pair (big for deep, wide trees) |
| ID path + `LIKE` | One hierarchy CTE, also gives you the path | String building, `LIKE` self-join is slow on large tables |

Both versions were run on SQL Server and return the output above.

---

## 🧪 Practice Scenarios

### Scenario 1: Multiple Root Nodes
What if there are multiple CEOs (multiple NULL managers)?
- The query handles this automatically — all roots become Level 0

### Scenario 2: Orphan Employees
What if an employee references a non-existent manager?
- They won't appear in results (no match in the JOIN)
- Add a LEFT JOIN variation to find orphans

### Scenario 3: Deep Hierarchies
What if the org has 50+ levels?
- Most RDBMS have default recursion limits
- SQL Server: `OPTION (MAXRECURSION 0)` for unlimited
- PostgreSQL: Works by default, but watch performance

---

## 📚 Key Concepts Summary

| Concept | Description |
|---------|-------------|
| **Recursive CTE** | A CTE that references itself to process hierarchical/graph data |
| **Anchor Member** | The base case that starts the recursion (non-recursive SELECT) |
| **Recursive Member** | The part that references the CTE itself and builds upon previous results |
| **Self-Referencing Table** | A table with a foreign key pointing to its own primary key |
| **UNION ALL** | Combines anchor + recursive results without duplicate elimination |
| **Termination** | Recursion stops when the recursive member returns no rows |

---

## 🔗 Related Topics to Explore

- **Bill of Materials (BOM) queries** — similar pattern for product assemblies
- **Graph traversal in SQL** — paths, cycles, shortest path
- **Nested Sets Model** — alternative to adjacency list for hierarchies
- **Closure Table Pattern** — pre-computed ancestor/descendant relationships
- **CONNECT BY (Oracle)** — Oracle's proprietary hierarchical query syntax

---

## 📅 Exercise Metadata

- **Difficulty:** Intermediate
- **Topics:** Recursive CTEs, Self-Joins, Hierarchical Data
- **SQL Standard:** SQL:1999 (Recursive CTEs)
- **Supported Databases:** PostgreSQL, SQL Server, MySQL 8.0+, Oracle, SQLite 3.8.3+

---

*Created as a learning exercise — practice makes perfect!*
