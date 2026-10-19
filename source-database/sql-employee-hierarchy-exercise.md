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

```sql
WITH EmployeeHierarchy AS (
    SELECT EmployeeID, Name, ManagerID, 0 AS Level
    FROM Employees
    WHERE ManagerID IS NULL
    
    UNION ALL
    
    SELECT e.EmployeeID, e.Name, e.ManagerID, eh.Level + 1
    FROM Employees e
    JOIN EmployeeHierarchy eh ON e.ManagerID = eh.EmployeeID
)
SELECT 
    e.EmployeeID,
    e.Name,
    COUNT(CASE WHEN eh.Level = e.Level + 1 THEN 1 END) AS DirectReports,
    COUNT(*) - 1 AS TotalSubordinates  -- -1 to exclude self
FROM EmployeeHierarchy e
LEFT JOIN EmployeeHierarchy eh ON eh.Path LIKE e.Path + '%'
GROUP BY e.EmployeeID, e.Name, e.Level
ORDER BY e.Level;
```

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
