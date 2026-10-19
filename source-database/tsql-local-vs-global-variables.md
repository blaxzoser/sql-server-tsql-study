# Q167. What are Local and Global Variables and their differences? (T-SQL / SQL Server)

> Study notes for interview prep. Covers the original answer, a corrected answer, runnable samples, a comparison table, common mistakes, and likely follow-up questions.

---

## 1. The original answer (as found in the question list)

> **Local variables** are the variables which can be used or exist inside the function. They are not known to the other functions and those variables cannot be referred or used. Variables can be created whenever that function is called.
>
> **Global variables** are the variables which can be used or exist throughout the program. Same variable declared in global cannot be used in functions. Global variables cannot be created whenever that function is called.

### What's right and what's unclear about it

| Statement | Verdict | Notes |
|---|---|---|
| Local variables exist only inside the function | ✅ Mostly right | In T-SQL the scope is the **batch, stored procedure, or function** where it's declared, not only "functions". |
| Not known to other functions | ✅ Right | A called procedure can't see the caller's variables. You must pass them as parameters. |
| Created whenever the function is called | ✅ Right | Each execution gets a fresh copy, starting as `NULL`. |
| Global variables exist throughout the program | ⚠️ Partly | In SQL Server they're **system-maintained** values (`@@ROWCOUNT`, `@@VERSION`…). Some are server-wide, many are per-session/connection. |
| "Same variable declared in global cannot be used in functions" | ❌ Confusing | You **can't declare** a global variable at all. You *can* read many `@@` values inside procedures and batches. |
| Global variables cannot be created | ✅ Right (in T-SQL) | Users cannot create them. They are read-only and maintained by SQL Server. |

---

## 2. Improved answer (say this in an interview)

> **Local variables** in T-SQL are user-defined variables declared with `DECLARE` and a single `@` prefix (for example `@Total`). Their scope is limited to the batch, stored procedure, or function in which they are declared. They start as `NULL`, can be assigned with `SET` or `SELECT`, and are destroyed when the batch or procedure ends. They are not visible to other procedures or to later batches (after `GO`).
>
> **Global variables** are system values with a `@@` prefix (for example `@@ROWCOUNT`, `@@ERROR`, `@@IDENTITY`, `@@VERSION`). They are created and maintained by SQL Server, are **read-only**, and cannot be declared or assigned by users. Microsoft's current documentation calls them **system functions**, but "global variables" is the older, still widely used name.
>
> **Key difference:** local variables are created and controlled by the developer within a limited scope; global variables are provided by SQL Server, are read-only, and give information about the server, the session, or the last statement executed.

---

## 3. Quick comparison

| | Local variable (`@`) | Global variable (`@@`) |
|---|---|---|
| Prefix | `@` | `@@` |
| Created by | Developer, with `DECLARE` | SQL Server |
| Can be assigned? | Yes (`SET` / `SELECT`) | No, read-only |
| Scope | Current batch, procedure, or function | Server-wide or session-wide (depends on the variable) |
| Lifetime | Until the batch/procedure ends | Maintained by the server |
| Initial value | `NULL` | Whatever SQL Server reports |
| Official name today | Local variable | System function |
| Examples | `@Total`, `@Name`, `@StartDate` | `@@ROWCOUNT`, `@@ERROR`, `@@IDENTITY`, `@@TRANCOUNT` |

---

## 4. Samples: Local variables

### 4.1 Declare, assign, use

```sql
DECLARE @EmpCount INT;
DECLARE @DeptName VARCHAR(50) = 'Sales';   -- declare + assign in one step (SQL Server 2008+)

SELECT @EmpCount = COUNT(*)
FROM Employees
WHERE Department = @DeptName;

PRINT 'Employees in ' + @DeptName + ': ' + CAST(@EmpCount AS VARCHAR(10));
```

### 4.2 Declaring several at once

```sql
DECLARE @FirstName VARCHAR(50) = 'Ana',
        @LastName  VARCHAR(50) = 'López',
        @Age       INT         = 30;

SELECT @FirstName AS FirstName, @LastName AS LastName, @Age AS Age;
```

### 4.3 `SET` vs `SELECT` for assignment

```sql
DECLARE @A INT, @B INT;

-- SET: one variable per statement (ANSI standard)
SET @A = 1;
SET @B = 2;

-- SELECT: can assign several at once
SELECT @A = 10, @B = 20;

-- SELECT can assign from a query
SELECT @A = MIN(Salary), @B = MAX(Salary) FROM Employees;
```

### 4.4 Scope ends with the batch (`GO`)

```sql
DECLARE @x INT = 10;
PRINT @x;          -- 10
GO                 -- batch ends here, @x is destroyed

PRINT @x;          -- ERROR: Must declare the scalar variable "@x".
```

### 4.5 A called procedure can't see the caller's variables

```sql
CREATE PROCEDURE ShowValue
AS
BEGIN
    PRINT @Val;    -- ERROR when creating: Must declare the scalar variable "@Val".
END
GO

-- Correct approach: pass it as a parameter
CREATE PROCEDURE ShowValue2 @Val INT
AS
BEGIN
    PRINT @Val;
END
GO

DECLARE @Val INT = 99;
EXEC ShowValue2 @Val = @Val;   -- prints 99
```

### 4.6 Local variable inside a stored procedure

```sql
CREATE PROCEDURE GetDeptSummary @Dept VARCHAR(50)
AS
BEGIN
    DECLARE @Total INT, @AvgSalary DECIMAL(10,2);   -- local to this procedure

    SELECT @Total = COUNT(*), @AvgSalary = AVG(Salary)
    FROM Employees
    WHERE Department = @Dept;

    SELECT @Dept AS Department, @Total AS Employees, @AvgSalary AS AvgSalary;
END
GO

EXEC GetDeptSummary @Dept = 'IT';
```

### 4.7 Table variable (also a local variable)

```sql
DECLARE @TopEarners TABLE (
    EmpId  INT,
    Name   VARCHAR(100),
    Salary DECIMAL(10,2)
);

INSERT INTO @TopEarners (EmpId, Name, Salary)
SELECT TOP 5 EmpId, Name, Salary
FROM Employees
ORDER BY Salary DESC;

SELECT * FROM @TopEarners;
```

---

## 5. Samples: Global variables (`@@` system values)

### 5.1 Server and session information

```sql
SELECT @@VERSION     AS SqlServerVersion,
       @@SERVERNAME  AS ServerName,
       @@SPID        AS MySessionId,
       @@LANGUAGE    AS SessionLanguage;
```

### 5.2 They can't be created or assigned

```sql
-- SET @@ROWCOUNT = 5;       -- ERROR: system values can't be assigned

-- DECLARE @@MyGlobal INT;   -- This does NOT create a global variable.
                             -- It's just a LOCAL variable with a confusing name.
                             -- Microsoft recommends never naming variables with @@.
```

### 5.3 Common globals in action

```sql
UPDATE Employees
SET Salary = Salary * 1.10
WHERE Department = 'Sales';

PRINT 'Rows updated: ' + CAST(@@ROWCOUNT AS VARCHAR(10));

INSERT INTO Orders (CustomerId, OrderDate) VALUES (5, GETDATE());
PRINT 'New order id: ' + CAST(SCOPE_IDENTITY() AS VARCHAR(10));   -- preferred over @@IDENTITY

BEGIN TRAN;
PRINT 'Open transactions: ' + CAST(@@TRANCOUNT AS VARCHAR(10));    -- 1
ROLLBACK;
PRINT 'Open transactions: ' + CAST(@@TRANCOUNT AS VARCHAR(10));    -- 0
```

### 5.4 `@@FETCH_STATUS` with a cursor

```sql
DECLARE @Name VARCHAR(100);

DECLARE emp_cursor CURSOR FOR
    SELECT Name FROM Employees WHERE Department = 'IT';

OPEN emp_cursor;
FETCH NEXT FROM emp_cursor INTO @Name;

WHILE @@FETCH_STATUS = 0          -- 0 = row fetched successfully
BEGIN
    PRINT @Name;
    FETCH NEXT FROM emp_cursor INTO @Name;
END

CLOSE emp_cursor;
DEALLOCATE emp_cursor;
```

### 5.5 Most-used global variables

| Global | Returns |
|---|---|
| `@@ROWCOUNT` | Rows affected by the last statement |
| `@@ERROR` | Error number of the last statement (0 = no error) |
| `@@IDENTITY` | Last identity value inserted in the session (any scope, includes triggers) |
| `@@TRANCOUNT` | Number of open transactions in the session |
| `@@FETCH_STATUS` | Status of the last cursor `FETCH` (0 = success) |
| `@@SPID` | Current session ID |
| `@@VERSION` | SQL Server version and OS info |
| `@@SERVERNAME` | Name of the local server |
| `@@LANGUAGE` | Current session language |
| `@@DATEFIRST` | First day of the week setting |
| `@@NESTLEVEL` | Nesting level of the current stored procedure execution |
| `@@MAX_CONNECTIONS` | Maximum simultaneous connections allowed |

---

## 6. Common mistakes

### 6.1 Reading `@@ROWCOUNT` / `@@ERROR` too late
They reset after **every** statement, including `PRINT` and `IF`.

```sql
UPDATE Employees SET Salary = Salary + 100 WHERE Department = 'IT';
PRINT 'Done';                 -- resets @@ROWCOUNT
SELECT @@ROWCOUNT;            -- returns 0, not the update count

-- ✅ Capture it immediately
UPDATE Employees SET Salary = Salary + 100 WHERE Department = 'IT';
DECLARE @Rows INT = @@ROWCOUNT;
PRINT 'Updated ' + CAST(@Rows AS VARCHAR(10)) + ' rows';
```

> For errors, prefer `TRY...CATCH` with `ERROR_NUMBER()` and `ERROR_MESSAGE()` over checking `@@ERROR` after each statement.

### 6.2 Using `@@IDENTITY` instead of `SCOPE_IDENTITY()`
If a trigger inserts into another table with an identity column, `@@IDENTITY` returns the **trigger's** value, not yours.

```sql
INSERT INTO Orders (CustomerId) VALUES (5);
SELECT SCOPE_IDENTITY();   -- ✅ identity from YOUR insert, current scope
SELECT @@IDENTITY;         -- ⚠️ may return an identity generated by a trigger
```

### 6.3 Forgetting a local starts as `NULL`
`NULL` + string = `NULL`, so the whole message disappears.

```sql
DECLARE @Name VARCHAR(50);            -- NULL
PRINT 'Hello ' + @Name;               -- prints an empty line

-- ✅ Initialize, or use ISNULL / CONCAT
PRINT 'Hello ' + ISNULL(@Name, 'guest');
PRINT CONCAT('Hello ', @Name);        -- CONCAT treats NULL as empty string
```

### 6.4 Declaring `VARCHAR` without a length
Without a length, `DECLARE` defaults to **1 character** (and `CAST` defaults to 30).

```sql
DECLARE @Msg VARCHAR = 'Hello world';
PRINT @Msg;                           -- prints: H

-- ✅ Always specify the length
DECLARE @Msg2 VARCHAR(100) = 'Hello world';
```

### 6.5 `SELECT` assignment when the query returns many rows or none

```sql
DECLARE @Name VARCHAR(100) = 'initial';

-- Many rows: @Name silently gets the value from the LAST row processed
SELECT @Name = Name FROM Employees;

-- No rows: @Name KEEPS its previous value (it does not become NULL)
SELECT @Name = Name FROM Employees WHERE 1 = 0;
PRINT @Name;                          -- still the old value

-- SET with a subquery behaves differently:
SET @Name = (SELECT Name FROM Employees WHERE 1 = 0);  -- becomes NULL
SET @Name = (SELECT Name FROM Employees);              -- ERROR if more than one row
```

### 6.6 Expecting a variable to survive `GO`
`GO` ends the batch, and every local variable with it (see sample 4.4). Re-declare it or use a temp table.

### 6.7 Expecting a variable to be visible in dynamic SQL
Dynamic SQL runs in its own batch.

```sql
DECLARE @Dept VARCHAR(50) = 'IT';
EXEC ('SELECT * FROM Employees WHERE Department = @Dept');   -- ERROR

-- ✅ Pass it with sp_executesql
EXEC sp_executesql
     N'SELECT * FROM Employees WHERE Department = @Dept',
     N'@Dept VARCHAR(50)',
     @Dept = @Dept;
```

### 6.8 Using a variable as a table or column name

```sql
DECLARE @Table SYSNAME = 'Employees';
SELECT * FROM @Table;           -- ERROR: treated as a table variable that doesn't exist

-- ✅ Use dynamic SQL, protected with QUOTENAME
DECLARE @sql NVARCHAR(MAX) = N'SELECT * FROM ' + QUOTENAME(@Table);
EXEC sp_executesql @sql;
```

### 6.9 Expecting `ROLLBACK` to undo variable changes
Variables (including table variables) are **not** affected by transactions.

```sql
DECLARE @Counter INT = 1;
DECLARE @Log TABLE (Msg VARCHAR(50));

BEGIN TRAN;
    SET @Counter = 100;
    INSERT INTO @Log VALUES ('inside transaction');
ROLLBACK;

SELECT @Counter;        -- 100, not 1
SELECT * FROM @Log;     -- the row is still there
```

> This is actually useful: a table variable can keep error-log rows even after a rollback.

### 6.10 Naming your own variables with `@@`
`DECLARE @@Total INT` is legal but it's still a **local** variable. It only causes confusion with system values. Use a single `@`.

---

## 7. Sharing values beyond a local variable's scope

Since locals die with the batch and you can't create true globals, these are the usual alternatives:

| Need | Option | Example |
|---|---|---|
| Pass a value to a procedure | Parameter | `EXEC MyProc @Id = @Id;` |
| Return a value from a procedure | `OUTPUT` parameter | see below |
| Share across batches in the same session | `#temp` table | `CREATE TABLE #Settings (...)` |
| Share across sessions | `##global` temp table | `CREATE TABLE ##Shared (...)` |
| Session key/value (SQL Server 2016+) | `SESSION_CONTEXT` | see below |

### 7.1 OUTPUT parameter

```sql
CREATE PROCEDURE GetEmpCount @Dept VARCHAR(50), @Count INT OUTPUT
AS
BEGIN
    SELECT @Count = COUNT(*) FROM Employees WHERE Department = @Dept;
END
GO

DECLARE @Result INT;
EXEC GetEmpCount @Dept = 'IT', @Count = @Result OUTPUT;
PRINT @Result;
```

### 7.2 Temp table across batches

```sql
CREATE TABLE #Settings (KeyName VARCHAR(50), Value VARCHAR(100));
INSERT INTO #Settings VALUES ('Region', 'North');
GO

SELECT Value FROM #Settings WHERE KeyName = 'Region';   -- still works after GO
```

### 7.3 SESSION_CONTEXT

```sql
EXEC sp_set_session_context @key = N'UserId', @value = 42;
GO

SELECT SESSION_CONTEXT(N'UserId') AS UserId;   -- 42, visible in later batches and procedures
```

---

## 8. Likely follow-up interview questions

**Q: What's the scope of a local variable in T-SQL?**
The batch, stored procedure, or function where it's declared. It ends at `GO` or when the procedure/function finishes.

**Q: Can you create your own global variable in SQL Server?**
No. `@@` values are system-defined and read-only. To share data across scopes use parameters, `OUTPUT` parameters, temp tables, or `SESSION_CONTEXT`.

**Q: What's the default value of a declared local variable?**
`NULL`.

**Q: Difference between `SET` and `SELECT` for assigning variables?**
`SET` assigns one variable and raises an error if a subquery returns more than one row; it sets `NULL` if no rows. `SELECT` can assign multiple variables at once, takes the last row if many are returned, and leaves the variable unchanged if none are returned.

**Q: Difference between `@@IDENTITY`, `SCOPE_IDENTITY()` and `IDENT_CURRENT('table')`?**
- `@@IDENTITY`: last identity in the session, any scope (triggers included).
- `SCOPE_IDENTITY()`: last identity in the session **and** current scope. Safest choice.
- `IDENT_CURRENT('table')`: last identity for that table, any session.

**Q: Why might `@@ROWCOUNT` return 0 after an UPDATE that changed rows?**
Another statement ran in between (even `PRINT` or `IF`) and reset it. Capture it right after the statement.

**Q: Does `ROLLBACK` undo changes to variables and table variables?**
No. Only real tables and temp tables are affected by transactions.

**Q: Table variable vs temp table?**
| | Table variable `@t` | Temp table `#t` |
|---|---|---|
| Scope | Batch/procedure | Session (until dropped or session ends) |
| Survives `GO` | No | Yes |
| Affected by `ROLLBACK` | No | Yes |
| Statistics | Limited | Yes, better for large data |
| Indexes | Only inline (PK, UNIQUE, inline INDEX) | Any index |
| Best for | Small row sets | Larger row sets, complex queries |

**Q: Can a variable be used in dynamic SQL?**
Not directly. Pass it as a parameter through `sp_executesql`.

---

## 9. Practice exercises

1. Declare `@Today DATE` initialized with `GETDATE()` and print it formatted as `yyyy-MM-dd`.
2. Write an `UPDATE` that raises salaries by 5% for one department and prints the number of affected rows correctly.
3. Insert a row into a table that has an identity column and a trigger, then compare `@@IDENTITY` and `SCOPE_IDENTITY()`.
4. Show that a variable declared before `GO` is not available after it, then fix it using a `#temp` table.
5. Create a procedure with an `OUTPUT` parameter that returns the highest salary in a given department.
6. Prove that `ROLLBACK` doesn't undo changes to a table variable.

---

## Appendix: same concept in other languages (for comparison)

The question is generic in many interview lists, so here's how it maps to general programming languages.

**C**

```c
#include <stdio.h>

int g = 0;                  // global: created once when the program starts

void counter() {
    int l = 0;              // local: recreated on each call
    l++;
    g++;
    printf("local = %d, global = %d\n", l, g);
}

int main() {
    counter();   // local = 1, global = 1
    counter();   // local = 1, global = 2
    return 0;
}
```

**Python**

```python
total = 0                   # global

def add(n):
    global total            # required to MODIFY a global in Python
    total += n

add(10)
print(total)                # 10
```

**Key contrast with T-SQL:** in C, Python and most languages, developers **can** declare their own global variables and functions can read and change them. In T-SQL, "global variables" are only the system `@@` values, which you can read but never create or assign.
