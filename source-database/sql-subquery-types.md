# SQL Interview Question 166: What Are the Types of Subquery?

Study notes with the answer, examples on a games database, common mistakes, follow-up interview questions and practice exercises.

---

## 1. The Question

> **Q166. What are the types of subquery?**

### Original answer (as found in the question bank)

There are two types of subquery: **Correlated** and **Non-Correlated**.

- A **correlated** subquery cannot be considered as an independent query, but it can refer to a column in a table listed in the FROM list of the main query.
- A **non-correlated** subquery can be considered as an independent query, and the output of the subquery is substituted in the main query.

### Improved answer (recommended for interviews)

There are two types of subquery: **non-correlated** and **correlated**.

- A **non-correlated subquery** is independent of the outer query. It runs **once**, and its result is substituted into the outer query. You can copy it out and run it on its own.
- A **correlated subquery** **references a column from the outer query**, so it cannot run on its own. Logically, it is re-evaluated **once for each row** the outer query processes.

**Why this version is better:** the original says a correlated subquery "can refer" to the outer query. In fact, referencing the outer query is exactly what *makes* it correlated. The improved version also mentions how many times each one runs, which is what interviewers usually want to hear.

### One-line answer (if asked to be brief)

> Non-correlated subqueries run once and don't depend on the outer query; correlated subqueries reference the outer query and run once per outer row.

---

## 2. Sample Database (runnable)

Use these tables to try every example in this file. The syntax works in MySQL, PostgreSQL, SQL Server and SQLite.

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

Useful facts about this data:

| Fact | Value |
|---|---|
| Average price of all games | 195 / 6 = **32.50** |
| Average price per genre | Shooter 27.50, RPG 50.00, Racing 20.00 |
| Games with no scores | Dragon Lore, Turbo Drift |
| Tie for top score | Star Siege: Marco and Luis both have 2000 |

---

## 3. Non-Correlated Subquery (Independent)

**How it works:** the inner query runs **once**, produces a value or a list, and the outer query uses that result.

### Example 1: Games priced above the overall average

```sql
SELECT title, price
FROM games
WHERE price > (SELECT AVG(price) FROM games);
```

The inner query returns `32.50` once. The outer query becomes `WHERE price > 32.50`.

**Result:**

| title | price |
|---|---|
| Dungeon Quest | 40.00 |
| Star Siege | 35.00 |
| Dragon Lore | 60.00 |

### Example 2: Games that have at least one recorded score

```sql
SELECT title
FROM games
WHERE game_id IN (SELECT game_id FROM scores);
```

The inner query returns the list `(1, 1, 2, 4, 4, 3)` once.

**Result:** Space Raiders, Dungeon Quest, Pixel Kart, Star Siege

### Example 3: Non-correlated subquery in the FROM clause (derived table)

```sql
SELECT genre, avg_price
FROM (SELECT genre, AVG(price) AS avg_price
      FROM games
      GROUP BY genre) AS genre_stats
WHERE avg_price > 25;
```

**Result:**

| genre | avg_price |
|---|---|
| Shooter | 27.50 |
| RPG | 50.00 |

---

## 4. Correlated Subquery (Dependent)

**How it works:** the inner query uses a column from the outer query (for example `g.genre`). It can't run alone, and it is re-evaluated for every row of the outer query.

### Example 1: Games priced above the average of their own genre

```sql
SELECT g.title, g.genre, g.price
FROM games g
WHERE g.price > (SELECT AVG(g2.price)
                 FROM games g2
                 WHERE g2.genre = g.genre);
```

Step by step for a few rows:

| Outer row | Inner query computes | Comparison | Kept? |
|---|---|---|---|
| Space Raiders (Shooter, 20) | AVG of Shooter = 27.50 | 20 > 27.50 | No |
| Star Siege (Shooter, 35) | AVG of Shooter = 27.50 | 35 > 27.50 | Yes |
| Dungeon Quest (RPG, 40) | AVG of RPG = 50.00 | 40 > 50.00 | No |
| Dragon Lore (RPG, 60) | AVG of RPG = 50.00 | 60 > 50.00 | Yes |
| Pixel Kart (Racing, 15) | AVG of Racing = 20.00 | 15 > 20.00 | No |
| Turbo Drift (Racing, 25) | AVG of Racing = 20.00 | 25 > 20.00 | Yes |

**Result:** Star Siege, Dragon Lore, Turbo Drift

### Example 2: Top scorer(s) for each game

```sql
SELECT s.player_name, s.game_id, s.points
FROM scores s
WHERE s.points = (SELECT MAX(s2.points)
                  FROM scores s2
                  WHERE s2.game_id = s.game_id);
```

**Result:**

| player_name | game_id | points |
|---|---|---|
| Luis | 1 | 1500 |
| Ana | 2 | 800 |
| Marco | 4 | 2000 |
| Luis | 4 | 2000 |
| Marco | 3 | 500 |

> Note: ties are returned. Game 4 has two top scorers. Interviewers like to ask about this.

### Example 3: Games nobody has played yet (EXISTS / NOT EXISTS)

```sql
SELECT g.title
FROM games g
WHERE NOT EXISTS (SELECT 1
                  FROM scores s
                  WHERE s.game_id = g.game_id);
```

**Result:** Dragon Lore, Turbo Drift

`EXISTS` only checks whether at least one row comes back; it doesn't care what is selected, so `SELECT 1` is a common convention.

### Example 4: Correlated subquery in the SELECT list

```sql
SELECT g.title,
       (SELECT COUNT(*)
        FROM scores s
        WHERE s.game_id = g.game_id) AS times_played
FROM games g;
```

**Result:**

| title | times_played |
|---|---|
| Space Raiders | 2 |
| Dungeon Quest | 1 |
| Pixel Kart | 1 |
| Star Siege | 2 |
| Dragon Lore | 0 |
| Turbo Drift | 0 |

---

## 5. How to Tell Them Apart

**The copy-paste test:** copy the inner query and run it alone.

- It runs fine → **non-correlated**
- It fails because it mentions an outer alias (like `g.genre`) → **correlated**

| Feature | Non-Correlated | Correlated |
|---|---|---|
| Depends on outer query? | No | Yes |
| Can run on its own? | Yes | No |
| How often it runs (logically) | Once | Once per outer row |
| Typical operators | `=`, `>`, `IN`, `ANY`, `ALL` | `EXISTS`, `NOT EXISTS`, `=` with a per-row aggregate |
| Typical use | Compare to a global value or list | Compare each row to its own group |
| Performance | Usually cheap | Can be slow on large tables if not optimized |

---

## 6. Other Ways Interviewers Classify Subqueries

Some interviewers expect a different classification. It helps to mention these after the main answer.

### By what the subquery returns

| Type | Returns | Example operator |
|---|---|---|
| Scalar / single-row | One value | `=`, `>`, `<` |
| Multi-row | One column, many rows | `IN`, `ANY`, `ALL` |
| Multi-column | Several columns | `(col1, col2) IN (...)` |
| Table subquery | A full result set | Used in `FROM` (derived table) |

Multi-column example (MySQL, PostgreSQL; not SQL Server):

```sql
SELECT title, genre, price
FROM games
WHERE (genre, price) IN (SELECT genre, MAX(price)
                         FROM games
                         GROUP BY genre);
```

**Result:** Star Siege, Dragon Lore, Turbo Drift (the most expensive game in each genre)

### By where the subquery appears

| Location | Name / use |
|---|---|
| `WHERE` | Filtering rows |
| `HAVING` | Filtering groups |
| `FROM` | Derived table / inline view |
| `SELECT` | Scalar subquery per row |

`HAVING` example:

```sql
-- Genres whose average price is above the overall average
SELECT genre, AVG(price) AS avg_price
FROM games
GROUP BY genre
HAVING AVG(price) > (SELECT AVG(price) FROM games);
```

**Result:** RPG, 50.00

### Nested subqueries

A subquery inside another subquery:

```sql
-- Players who scored in the most expensive game
SELECT player_name
FROM scores
WHERE game_id = (SELECT game_id
                 FROM games
                 WHERE price = (SELECT MAX(price) FROM games));
```

**Result:** no rows (Dragon Lore has no scores). A good reminder that empty results are valid.

---

## 7. Common Mistakes

### Mistake 1: Using `=` with a subquery that returns several rows

```sql
-- WRONG: error, subquery returns more than one row
SELECT title FROM games
WHERE game_id = (SELECT game_id FROM scores);

-- RIGHT
SELECT title FROM games
WHERE game_id IN (SELECT game_id FROM scores);
```

Use `=` only when the subquery is guaranteed to return one value.

### Mistake 2: `NOT IN` with NULL values

If the subquery returns even one `NULL`, `NOT IN` returns **no rows at all**.

```sql
INSERT INTO scores VALUES (7, NULL, 'Guest', 100);

-- Returns NOTHING because the list contains NULL
SELECT title FROM games
WHERE game_id NOT IN (SELECT game_id FROM scores);

-- Works correctly
SELECT g.title FROM games g
WHERE NOT EXISTS (SELECT 1 FROM scores s WHERE s.game_id = g.game_id);
```

**Why:** `x NOT IN (1, 2, NULL)` means `x <> 1 AND x <> 2 AND x <> NULL`, and `x <> NULL` is UNKNOWN, so the whole condition is never TRUE.

**Rule:** prefer `NOT EXISTS` over `NOT IN`, or filter `WHERE game_id IS NOT NULL` inside the subquery.

### Mistake 3: Missing aliases accidentally "un-correlates" the query

```sql
-- WRONG: genre = genre compares the inner table to itself
SELECT title, genre, price
FROM games
WHERE price > (SELECT AVG(price) FROM games WHERE genre = genre);
```

Inside the subquery, both `genre` columns resolve to the inner `games` table, so the condition is always true. The query silently becomes "above the overall average." No error, wrong result.

**Fix:** always alias both tables (`g` and `g2`) and qualify every column.

### Mistake 4: Forgetting the alias on a derived table

```sql
-- WRONG in MySQL and SQL Server
SELECT * FROM (SELECT genre, AVG(price) AS avg_price FROM games GROUP BY genre);

-- RIGHT
SELECT * FROM (SELECT genre, AVG(price) AS avg_price FROM games GROUP BY genre) AS genre_stats;
```

### Mistake 5: Not handling ties

"Top scorer per game" with a correlated `MAX` returns **all** tied players. If the interviewer wants exactly one per game, you need a tie-breaker (for example `ROW_NUMBER()`).

### Mistake 6: Assuming correlated subqueries are always slow

Logically they run once per row, but modern optimizers often rewrite them as joins internally. Check the execution plan instead of assuming.

### Mistake 7: Calling every nested query "correlated"

Nested ≠ correlated. A query can be deeply nested and still be non-correlated. What matters is whether the inner query references the outer query.

### Mistake 8: `ORDER BY` inside a subquery

In most cases it's pointless (the outer query decides final order), and SQL Server rejects it unless you also use `TOP`, `OFFSET` or `FOR XML`.

### Mistake 9: Scalar subquery in SELECT returning more than one row

```sql
-- WRONG if a game has several scores
SELECT title, (SELECT points FROM scores s WHERE s.game_id = g.game_id) AS pts
FROM games g;
```

Use an aggregate (`MAX`, `COUNT`, `SUM`) or a `LIMIT 1` / `TOP 1` with `ORDER BY`.

---

## 8. Rewriting Correlated Subqueries (Performance)

Interviewers often follow up with: "How would you rewrite this without a correlated subquery?"

### Option A: JOIN with a derived table

```sql
SELECT g.title, g.genre, g.price
FROM games g
JOIN (SELECT genre, AVG(price) AS avg_price
      FROM games
      GROUP BY genre) a
  ON a.genre = g.genre
WHERE g.price > a.avg_price;
```

The genre averages are computed once, then joined.

### Option B: Window function

```sql
SELECT title, genre, price
FROM (SELECT title, genre, price,
             AVG(price) OVER (PARTITION BY genre) AS genre_avg
      FROM games) t
WHERE price > genre_avg;
```

### Option C: LEFT JOIN instead of NOT EXISTS

```sql
SELECT g.title
FROM games g
LEFT JOIN scores s ON s.game_id = g.game_id
WHERE s.game_id IS NULL;
```

### Exactly one top scorer per game (handles ties)

```sql
SELECT player_name, game_id, points
FROM (SELECT player_name, game_id, points,
             ROW_NUMBER() OVER (PARTITION BY game_id
                                ORDER BY points DESC, player_name) AS rn
      FROM scores) t
WHERE rn = 1;
```

### Performance tips

- Index the columns used in the correlation condition (here `scores.game_id`, `games.genre`).
- Compare execution plans (`EXPLAIN` in MySQL/PostgreSQL, "Display Estimated Execution Plan" in SQL Server).
- `EXISTS` stops at the first match, so it's often efficient for "does any row exist" checks.

---

## 9. Likely Follow-Up Interview Questions

**Q: Which runs first, the inner or the outer query?**
For a non-correlated subquery, the inner query runs first (once). For a correlated subquery, the outer query drives it: for each outer row, the inner query is evaluated.

**Q: What is the difference between `IN` and `EXISTS`?**
`IN` compares a value against a list returned by the subquery. `EXISTS` only checks whether the subquery returns any row. `EXISTS` is usually correlated, handles NULLs safely in the `NOT` form, and can stop at the first match.

**Q: Can a subquery be used in `INSERT`, `UPDATE` or `DELETE`?**
Yes.

```sql
-- Delete scores for games that no longer exist
DELETE FROM scores
WHERE game_id NOT IN (SELECT game_id FROM games WHERE game_id IS NOT NULL);

-- Raise RPG prices to the RPG average where below it (correlated UPDATE)
UPDATE games g
SET price = (SELECT AVG(g2.price) FROM games g2 WHERE g2.genre = g.genre)
WHERE g.genre = 'RPG';
```

(Note: MySQL doesn't allow selecting from the same table you're updating in a subquery; wrap it in a derived table there.)

**Q: Subquery vs JOIN, which is better?**
Neither is always better. Joins are often faster and clearer for combining columns from several tables. Subqueries are clearer for filtering ("rows where something exists / is above X"). Modern optimizers frequently produce the same plan for both.

**Q: What are `ANY` and `ALL`?**

```sql
-- Games more expensive than ALL racing games (> 25)
SELECT title FROM games
WHERE price > ALL (SELECT price FROM games WHERE genre = 'Racing');
-- Result: Dungeon Quest, Star Siege, Dragon Lore

-- Games more expensive than ANY racing game (> 15)
SELECT title FROM games
WHERE price > ANY (SELECT price FROM games WHERE genre = 'Racing');
-- Result: Space Raiders, Dungeon Quest, Star Siege, Dragon Lore, Turbo Drift
```

**Q: What is a CTE and how does it relate to subqueries?**
A CTE (`WITH ... AS`) is a named subquery defined before the main query. It improves readability and can be referenced several times.

```sql
WITH genre_stats AS (
    SELECT genre, AVG(price) AS avg_price
    FROM games
    GROUP BY genre
)
SELECT g.title, g.price, gs.avg_price
FROM games g
JOIN genre_stats gs ON gs.genre = g.genre
WHERE g.price > gs.avg_price;
```

**Q: What is the maximum nesting level of subqueries?**
It depends on the database (SQL Server allows up to 32 levels). In practice, deep nesting should be replaced with CTEs for readability.

---

## 10. Practice Exercises

Try each one before reading the solution. All use the sample data from section 2.

**Exercise 1.** List the cheapest game(s) overall. *(Non-correlated)*

<details><summary>Solution</summary>

```sql
SELECT title, price FROM games
WHERE price = (SELECT MIN(price) FROM games);
```
Result: Pixel Kart, 15.00
</details>

**Exercise 2.** List the cheapest game in each genre. *(Correlated)*

<details><summary>Solution</summary>

```sql
SELECT g.title, g.genre, g.price
FROM games g
WHERE g.price = (SELECT MIN(g2.price) FROM games g2 WHERE g2.genre = g.genre);
```
Result: Space Raiders, Dungeon Quest, Pixel Kart
</details>

**Exercise 3.** List players who have scored more than 1000 points in any game. Show each player once.

<details><summary>Solution</summary>

```sql
SELECT DISTINCT player_name
FROM scores
WHERE points > 1000;
```
Result: Ana, Luis, Marco

(Trick question: no subquery needed. Interviewers sometimes check whether you over-complicate.)
</details>

**Exercise 4.** List games that Ana has played. *(Non-correlated with IN)*

<details><summary>Solution</summary>

```sql
SELECT title FROM games
WHERE game_id IN (SELECT game_id FROM scores WHERE player_name = 'Ana');
```
Result: Space Raiders, Dungeon Quest
</details>

**Exercise 5.** List scores that are above the average score for that same game. *(Correlated)*

<details><summary>Solution</summary>

```sql
SELECT s.player_name, s.game_id, s.points
FROM scores s
WHERE s.points > (SELECT AVG(s2.points) FROM scores s2 WHERE s2.game_id = s.game_id);
```
Result: Luis, game 1, 1500 (game 1 average is 1350; the other games have one score or a tie, so nobody is strictly above average)
</details>

**Exercise 6.** Rewrite Exercise 5 with a window function.

<details><summary>Solution</summary>

```sql
SELECT player_name, game_id, points
FROM (SELECT player_name, game_id, points,
             AVG(points) OVER (PARTITION BY game_id) AS game_avg
      FROM scores) t
WHERE points > game_avg;
```
</details>

**Exercise 7.** Spot the bug: why does this return every game?

```sql
SELECT title FROM games
WHERE EXISTS (SELECT 1 FROM scores WHERE game_id = game_id);
```

<details><summary>Solution</summary>

Both `game_id` references resolve to `scores`, so the condition is always true for non-NULL rows and the subquery isn't correlated. `EXISTS` is therefore true for every game. Fix:

```sql
SELECT g.title FROM games g
WHERE EXISTS (SELECT 1 FROM scores s WHERE s.game_id = g.game_id);
```
</details>

---

## 11. Cheat Sheet

- **Two main types:** non-correlated (independent, runs once) and correlated (references outer query, runs per row).
- **Test:** can the inner query run alone? Yes = non-correlated.
- **Correlated favorites:** `EXISTS`, `NOT EXISTS`, "compare each row to its own group."
- **Always alias** tables in correlated subqueries and qualify every column.
- **Avoid `NOT IN`** when NULLs are possible; use `NOT EXISTS`.
- **`=` needs one value;** use `IN` for lists.
- **Alternatives:** JOIN + GROUP BY, window functions, CTEs.
- **Other classifications:** scalar / multi-row / multi-column / table; WHERE / HAVING / FROM / SELECT.
