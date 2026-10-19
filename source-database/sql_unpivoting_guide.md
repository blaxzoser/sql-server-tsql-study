# SQL Unpivoting Guide: Transforming Columns into Rows

A comprehensive guide covering SQL unpivoting techniques using `UNION ALL`, with practical examples, common mistakes, and important logical concepts.

---

## Table of Contents

1. [What is Unpivoting?](#what-is-unpivoting)
2. [Problem 1: Address Lines](#problem-1-address-lines)
3. [Problem 2: Contact Phone Numbers](#problem-2-contact-phone-numbers)
4. [Common Mistakes](#common-mistakes)
5. [OR vs AND Logic in SQL](#or-vs-and-logic-in-sql)
6. [Key Takeaways](#key-takeaways)

---

## What is Unpivoting?

**Unpivoting** is the process of transforming data from a **wide format** (multiple columns) to a **long format** (multiple rows). This is useful when you have related data spread across multiple columns and need to normalize it.

### Wide Format (Before):
| MemberID | Address1 | Address2 | Address3 |
|----------|----------|----------|----------|
| 1 | 123 Main St | Apt 4B | NULL |

### Long Format (After):
| MemberID | LineNumber | AddressLine |
|----------|------------|-------------|
| 1 | 1 | 123 Main St |
| 1 | 2 | Apt 4B |

---

## Problem 1: Address Lines

### Scenario

Each member record has four separate columns for address lines:
- `MailingAddressLine1`
- `MailingAddressLine2`
- `MailingAddressLine3`
- `MailingAddressLine4`

### Requirements

| Requirement | Description |
|-------------|-------------|
| Transformation | Unpivot so each non-null address line appears as its own row |
| Output Columns | `MemberID`, `LineNumber` (1-4), `AddressLine`, `City`, `State`, `ZipCode` |
| Filters | Exclude NULL address lines, Exclude NULL MemberID |

### Solution

```sql
WITH AddressLines AS (
    SELECT 
        MemberID, 
        1 AS LineNumber, 
        MailingAddressLine1 AS AddressLine,
        City, State, ZipCode
    FROM MemberAddress
    WHERE MailingAddressLine1 IS NOT NULL
    
    UNION ALL
    
    SELECT 
        MemberID, 
        2 AS LineNumber, 
        MailingAddressLine2 AS AddressLine,
        City, State, ZipCode
    FROM MemberAddress
    WHERE MailingAddressLine2 IS NOT NULL
    
    UNION ALL
    
    SELECT 
        MemberID, 
        3 AS LineNumber, 
        MailingAddressLine3 AS AddressLine,
        City, State, ZipCode
    FROM MemberAddress
    WHERE MailingAddressLine3 IS NOT NULL
    
    UNION ALL
    
    SELECT 
        MemberID, 
        4 AS LineNumber, 
        MailingAddressLine4 AS AddressLine,
        City, State, ZipCode
    FROM MemberAddress
    WHERE MailingAddressLine4 IS NOT NULL
)
SELECT 
    MemberID,
    LineNumber, 
    AddressLine,
    City, 
    State, 
    ZipCode
FROM AddressLines
WHERE MemberID IS NOT NULL
ORDER BY MemberID, LineNumber;
```

### Why Use a CTE (WITH clause)?

- Cleaner organization of the UNION ALL blocks
- Easier to apply final filters (like `MemberID IS NOT NULL`)
- Better readability and maintainability

---

## Problem 2: Contact Phone Numbers

### Scenario

Customer contact information is stored with multiple phone columns:
- `Phone_Home`
- `Phone_Work`
- `Phone_Mobile`
- `Phone_Fax`

### Requirements

| Requirement | Description |
|-------------|-------------|
| Transformation | Unpivot so each non-null phone number appears as its own row |
| Output Columns | `CustomerID`, `PhoneType`, `PhoneNumber`, `CustomerName`, `Email` |
| Filters | Exclude NULL or empty phone numbers, Exclude NULL CustomerID |

### Sample Source Data

| CustomerID | CustomerName | Phone_Home | Phone_Work | Phone_Mobile | Phone_Fax | Email |
|------------|--------------|------------|------------|--------------|-----------|-------|
| 101 | John Smith | 555-1234 | NULL | 555-5678 | NULL | john@email.com |
| 102 | Jane Doe | NULL | 555-9999 | 555-8888 | 555-7777 | jane@email.com |

### Expected Output

| CustomerID | PhoneType | PhoneNumber | CustomerName | Email |
|------------|-----------|-------------|--------------|-------|
| 101 | Home | 555-1234 | John Smith | john@email.com |
| 101 | Mobile | 555-5678 | John Smith | john@email.com |
| 102 | Work | 555-9999 | Jane Doe | jane@email.com |
| 102 | Mobile | 555-8888 | Jane Doe | jane@email.com |
| 102 | Fax | 555-7777 | Jane Doe | jane@email.com |

### Solution

```sql
SELECT 
    CustomerID,
    'Home' AS PhoneType, 
    Phone_Home AS PhoneNumber,
    CustomerName,
    Email 
FROM CustomerContacts 
WHERE Phone_Home IS NOT NULL 
  AND Phone_Home <> ''
  AND CustomerID IS NOT NULL

UNION ALL

SELECT 
    CustomerID,
    'Work' AS PhoneType, 
    Phone_Work AS PhoneNumber,
    CustomerName,
    Email 
FROM CustomerContacts 
WHERE Phone_Work IS NOT NULL 
  AND Phone_Work <> ''
  AND CustomerID IS NOT NULL

UNION ALL

SELECT 
    CustomerID,
    'Mobile' AS PhoneType, 
    Phone_Mobile AS PhoneNumber,
    CustomerName,
    Email 
FROM CustomerContacts 
WHERE Phone_Mobile IS NOT NULL 
  AND Phone_Mobile <> ''
  AND CustomerID IS NOT NULL

UNION ALL

SELECT 
    CustomerID,
    'Fax' AS PhoneType, 
    Phone_Fax AS PhoneNumber,
    CustomerName,
    Email 
FROM CustomerContacts 
WHERE Phone_Fax IS NOT NULL 
  AND Phone_Fax <> ''
  AND CustomerID IS NOT NULL

ORDER BY CustomerID, PhoneType;
```

---

## Common Mistakes

### 1. Duplicate UNION ALL Blocks

**Problem:** Copy-pasting UNION blocks and forgetting to modify them.

```sql
-- WRONG: LineNumber 4 appears twice!
UNION ALL
SELECT MemberID, 4 AS LineNumber, MailingAddressLine4...
UNION ALL
SELECT MemberID, 4 AS LineNumber, MailingAddressLine4...  -- Duplicate!
```

**Result:** Data duplication in output.

**Fix:** Always review each UNION block for uniqueness.

---

### 2. Typo: `it` instead of `is`

**Problem:** Common typing error.

```sql
-- WRONG
WHERE CustomerID it not null

-- CORRECT
WHERE CustomerID IS NOT NULL
```

---

### 3. Using Alias in WHERE Clause

**Problem:** You cannot reference a column alias in the same query's WHERE clause.

```sql
-- WRONG (PhoneNumber is an alias, not the actual column)
SELECT Phone_Home AS PhoneNumber
FROM CustomerContacts
WHERE PhoneNumber IS NOT NULL

-- CORRECT (use the actual column name)
SELECT Phone_Home AS PhoneNumber
FROM CustomerContacts
WHERE Phone_Home IS NOT NULL
```

**Rule:** Aliases are created AFTER the WHERE clause is evaluated, so use original column names in WHERE.

---

### 4. Using OR Instead of AND for Exclusion Logic

**Problem:** Misunderstanding "exclude X or Y" requirements.

```sql
-- WRONG
WHERE Phone_Home IS NOT NULL OR Phone_Home <> ''

-- CORRECT
WHERE Phone_Home IS NOT NULL AND Phone_Home <> ''
```

**See the detailed explanation in the next section.**

---

### 5. Missing ORDER BY

**Problem:** Results appear in unpredictable order.

```sql
-- Add ORDER BY for consistent, organized output
ORDER BY MemberID, LineNumber;
```

---

### 6. Forgetting to Change Column Names in Each Block

**Problem:** Copy-pasting without updating the column reference.

```sql
-- WRONG: Still referencing Phone_Home in the Work block
SELECT 'Work' AS PhoneType, Phone_Home AS PhoneNumber  -- Should be Phone_Work!

-- CORRECT
SELECT 'Work' AS PhoneType, Phone_Work AS PhoneNumber
```

---

## OR vs AND Logic in SQL

### The Core Confusion

**Requirement says:**
> "Exclude any rows where PhoneNumber is NULL **OR** empty string"

**But SQL uses:**
```sql
WHERE Phone_Home IS NOT NULL AND Phone_Home <> ''
```

**Why?**

---

### Understanding Include vs Exclude

| Perspective | Logic |
|-------------|-------|
| **EXCLUDE** (remove these) | NULL **OR** empty |
| **INCLUDE** (keep these) | NOT NULL **AND** NOT empty |

The `WHERE` clause defines what to **INCLUDE**, not what to exclude.

---

### De Morgan's Law

When you negate a condition with OR, it becomes AND:

```
NOT (A OR B)  =  (NOT A) AND (NOT B)
```

| Original (Exclude) | Negated (Include) |
|--------------------|-------------------|
| `NULL OR empty` | `NOT NULL AND NOT empty` |

---

### Practical Truth Table

| PhoneNumber | IS NOT NULL | <> '' (not empty) | Using OR | Using AND |
|-------------|-------------|-------------------|----------|-----------|
| `'555-1234'` | ✅ TRUE | ✅ TRUE | ✅ TRUE | ✅ **KEEP** |
| `NULL` | ❌ FALSE | ❌ FALSE* | ❌ FALSE | ❌ **EXCLUDE** |
| `''` (empty) | ✅ TRUE | ❌ FALSE | ✅ TRUE ❌ | ❌ **EXCLUDE** |

*NULL compared to anything returns NULL/FALSE

---

### The Problem with OR

```sql
WHERE Phone_Home IS NOT NULL OR Phone_Home <> ''
```

If `Phone_Home = ''` (empty string):
1. `IS NOT NULL` → **TRUE** (empty string is not null!)
2. `<> ''` → **FALSE**
3. `TRUE OR FALSE` → **TRUE**
4. ❌ **Empty string is INCLUDED** (wrong!)

---

### AND Fixes It

```sql
WHERE Phone_Home IS NOT NULL AND Phone_Home <> ''
```

If `Phone_Home = ''` (empty string):
1. `IS NOT NULL` → **TRUE**
2. `<> ''` → **FALSE**
3. `TRUE AND FALSE` → **FALSE**
4. ✅ **Empty string is EXCLUDED** (correct!)

---

### Quick Reference

| Requirement Says | SQL WHERE Uses |
|------------------|----------------|
| Exclude rows where A **OR** B | `WHERE NOT A AND NOT B` |
| Exclude rows where A **AND** B | `WHERE NOT A OR NOT B` |

**Memory trick:** "Exclude with OR" → "Include with AND"

---

## Key Takeaways

### When to Use UNION ALL for Unpivoting

✅ Use when:
- You have a fixed, known number of columns to unpivot
- Each column should become a separate row
- You need to assign identifiers (like LineNumber or PhoneType)

### UNION vs UNION ALL

| Operator | Behavior | Performance |
|----------|----------|-------------|
| `UNION` | Removes duplicates | Slower (sorts data) |
| `UNION ALL` | Keeps all rows | Faster |

**For unpivoting, always use `UNION ALL`** — you want every row, and duplicates in source data should remain.

### Best Practices Checklist

- [ ] Each UNION block references the correct source column
- [ ] Use actual column names in WHERE, not aliases
- [ ] Use AND for "exclude null or empty" logic
- [ ] Check for duplicate UNION blocks
- [ ] Include ORDER BY for consistent output
- [ ] Use CTEs for cleaner organization
- [ ] Verify each block has unique identifier values (LineNumber, PhoneType, etc.)

### Alternative Approaches

For databases that support it, you can also use:

**SQL Server UNPIVOT:**
```sql
SELECT CustomerID, PhoneType, PhoneNumber
FROM CustomerContacts
UNPIVOT (
    PhoneNumber FOR PhoneType IN (Phone_Home, Phone_Work, Phone_Mobile, Phone_Fax)
) AS unpvt;
```

**PostgreSQL LATERAL + VALUES:**
```sql
SELECT c.CustomerID, x.PhoneType, x.PhoneNumber
FROM CustomerContacts c
CROSS JOIN LATERAL (
    VALUES 
        ('Home', c.Phone_Home),
        ('Work', c.Phone_Work),
        ('Mobile', c.Phone_Mobile),
        ('Fax', c.Phone_Fax)
) AS x(PhoneType, PhoneNumber)
WHERE x.PhoneNumber IS NOT NULL;
```

However, the `UNION ALL` approach works in **all SQL databases** and is the most portable solution.

---

## Quick Reference Card

```
┌─────────────────────────────────────────────────────────────┐
│                 SQL UNPIVOTING CHEAT SHEET                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  PATTERN:                                                   │
│  ─────────                                                  │
│  SELECT ID, 'Type1' AS Type, Column1 AS Value FROM Table   │
│  WHERE Column1 IS NOT NULL                                  │
│  UNION ALL                                                  │
│  SELECT ID, 'Type2' AS Type, Column2 AS Value FROM Table   │
│  WHERE Column2 IS NOT NULL                                  │
│  ...                                                        │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  COMMON MISTAKES:                                           │
│  ────────────────                                           │
│  ✗ it not null        →  ✓ IS NOT NULL                     │
│  ✗ WHERE alias        →  ✓ WHERE actual_column             │
│  ✗ exclude with OR    →  ✓ include with AND                │
│  ✗ duplicate blocks   →  ✓ unique blocks only              │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  LOGIC RULE:                                                │
│  ───────────                                                │
│  "Exclude NULL or empty"                                    │
│        ↓                                                    │
│  WHERE col IS NOT NULL AND col <> ''                        │
│                                                             │
│  NOT (A OR B) = (NOT A) AND (NOT B)                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

*Guide created: October 2026*
