# 10. SQL: Aggregates, GROUP BY, Joins, Set Operations, Subqueries, Views

> **This is where SQL questions get tricky.** Almost every hard SQL MCQ hides one of three things: **NULL** handling in aggregates, **WHERE vs HAVING**, or **NOT IN with NULLs**. We'll trace every example by hand on the same tables as [Chapter 09](09_SQL_Basics_DDL_DML_DCL_TCL.md).

---

## 0. Our tables (same as Chapter 09)

**Emp**

| eid | name | dept | salary | mgr |
|---|---|---|---|---|
| 1 | Riya | CS | 70000 | NULL |
| 2 | Aman | CS | 50000 | 1 |
| 3 | Neha | EE | 60000 | 1 |
| 4 | Karan | EE | NULL | 3 |
| 5 | Divya | ME | 40000 | 3 |
| 6 | Arjun | CS | 50000 | 1 |

**Dept**

| dept | dname | building |
|---|---|---|
| CS | Computer | B1 |
| EE | Electrical | B2 |
| CE | Civil | B3 |

---

## 1. Aggregate functions

Five standard ones: **COUNT, SUM, AVG, MIN, MAX**.

### 1.1 The NULL rules

- **COUNT(\*)** counts **rows**, including rows with NULLs.
- **COUNT(column)** counts **non-NULL** values in that column.
- **SUM, AVG, MIN, MAX ignore NULLs.**
- On an empty set: COUNT returns **0**; SUM/AVG/MIN/MAX return **NULL**.

On Emp:

| Expression | Value | Why |
|---|---|---|
| COUNT(\*) | **6** | 6 rows |
| COUNT(salary) | **5** | Karan's NULL skipped |
| COUNT(mgr) | **5** | Riya's NULL skipped |
| COUNT(DISTINCT salary) | **4** | {70000, 50000, 60000, 40000} |
| COUNT(DISTINCT dept) | **3** | CS, EE, ME |
| SUM(salary) | **270000** | 70+50+60+40+50 thousand |
| AVG(salary) | **54000** | 270000 / **5** |
| SUM(salary)/COUNT(\*) | **45000** | 270000 / **6**: **different!** |
| MIN(salary), MAX(salary) | 40000, 70000 | |
| MIN(name) | 'Aman' | Works on strings (alphabetical) |

> **Trap.** AVG(col) = SUM(col)/COUNT(col), **not** SUM(col)/COUNT(\*), when NULLs exist.

SUM and AVG need numbers; MIN, MAX and COUNT work on strings and dates too.

### 1.2 Aggregates without GROUP BY

They collapse the **whole table** into **one row**. So you can't mix them with plain columns:

```sql
SELECT name, MAX(salary) FROM Emp;     -- INVALID in standard SQL: which name?
```

---

## 2. GROUP BY and HAVING

### 2.1 GROUP BY

Splits rows into groups with equal values, then computes aggregates per group.

```sql
SELECT dept, COUNT(*), COUNT(salary), AVG(salary)
FROM Emp
GROUP BY dept;
```

| dept | COUNT(\*) | COUNT(salary) | AVG(salary) |
|---|---|---|---|
| CS | 3 | 3 | 56666.67 |
| EE | 2 | 1 | 60000 |
| ME | 1 | 1 | 40000 |

(EE's average is 60000, not 30000: Karan's NULL is ignored.)

**The golden rule:** every column in SELECT (and HAVING) that is **not inside an aggregate** must appear in **GROUP BY**.

```sql
SELECT dept, name, COUNT(*) FROM Emp GROUP BY dept;   -- INVALID: name isn't grouped
```

NULLs in the grouping column form **one group** together.

### 2.2 WHERE vs HAVING

| | WHERE | HAVING |
|---|---|---|
| Filters | **Rows** | **Groups** |
| When | **Before** grouping | **After** grouping and aggregation |
| Aggregates allowed? | **No** | **Yes** |

```sql
SELECT dept, AVG(salary)
FROM Emp
WHERE salary > 45000          -- removes Divya (40000) and Karan (NULL -> UNKNOWN)
GROUP BY dept
HAVING COUNT(*) >= 2;         -- keeps groups with at least 2 remaining rows
```

Trace:
1. WHERE: Riya (CS, 70k), Aman (CS, 50k), Neha (EE, 60k), Arjun (CS, 50k).
2. GROUP BY: CS {70k, 50k, 50k}, EE {60k}.
3. HAVING COUNT(\*) ≥ 2: only CS.
4. SELECT: **(CS, 56666.67)**.

```sql
WHERE AVG(salary) > 50000     -- INVALID: aggregate in WHERE
```

---

## 3. String matching with LIKE

| Wildcard | Matches |
|---|---|
| `%` | **Zero or more** characters |
| `_` | **Exactly one** character |

| Pattern | Meaning | On Emp names |
|---|---|---|
| `'A%'` | Starts with A | Aman, Arjun |
| `'%a'` | Ends with a | Riya, Neha, Divya |
| `'%an%'` | Contains "an" | Aman, Karan |
| `'_____'` (5 underscores) | Exactly 5 characters | Karan, Divya, Arjun |
| `'____%'` (4 underscores + %) | **At least** 4 characters | All six |
| `'_a%'` | Second letter is a | Karan |

To match a literal `%` or `_`, use ESCAPE: `LIKE '50\%%' ESCAPE '\'` matches strings starting with "50%".

### Common string functions

`UPPER`, `LOWER`, `LENGTH` (bytes in some systems), `CHAR_LENGTH` (characters), `CONCAT` or `||`, `SUBSTRING(s, start, len)` (1-based), `TRIM/LTRIM/RTRIM`, `REPLACE`, `POSITION/INSTR`.

---

## 4. Set operations

```sql
(SELECT dept FROM Emp)  UNION      (SELECT dept FROM Dept);   -- CS, EE, ME, CE         (4)
(SELECT dept FROM Emp)  UNION ALL  (SELECT dept FROM Dept);   -- 6 + 3 = 9 rows
(SELECT dept FROM Emp)  INTERSECT  (SELECT dept FROM Dept);   -- CS, EE                 (2)
(SELECT dept FROM Emp)  EXCEPT     (SELECT dept FROM Dept);   -- ME                     (1)
(SELECT dept FROM Dept) EXCEPT     (SELECT dept FROM Emp);    -- CE                     (1)
```

- UNION, INTERSECT, EXCEPT (MINUS in Oracle) **remove duplicates**.
- The **ALL** versions keep them. With bags: if a value appears m times in A and n times in B, UNION ALL gives m + n, INTERSECT ALL gives min(m, n), EXCEPT ALL gives max(m − n, 0).
- Operands must be **union compatible** (same number of columns, compatible types).

---

## 5. Joins in SQL

### 5.1 Cartesian product

```sql
SELECT * FROM Emp, Dept;          -- 6 × 3 = 18 rows
SELECT * FROM Emp CROSS JOIN Dept;
```

### 5.2 Inner join

```sql
SELECT e.name, d.dname
FROM Emp e JOIN Dept d ON e.dept = d.dept;
```

Matches: Riya, Aman, Arjun (CS) and Neha, Karan (EE) → **5 rows**. Divya (ME) has no partner; CE has no employees.

Equivalent old style: `FROM Emp e, Dept d WHERE e.dept = d.dept`.

### 5.3 NATURAL JOIN and USING

```sql
SELECT * FROM Emp NATURAL JOIN Dept;     -- joins on ALL same-named columns (here: dept)
SELECT * FROM Emp JOIN Dept USING (dept);
```

Both show `dept` only **once**.

> **Trap.** NATURAL JOIN matches on **every** column with the same name. If both tables also had a column called `name` meaning different things, NATURAL JOIN would silently require those to match too. `USING (col)` lets you pick.

### 5.4 Outer joins

```sql
SELECT e.name, d.dname FROM Emp e LEFT JOIN Dept d ON e.dept = d.dept;
```
5 matched rows + (Divya, NULL) = **6 rows**.

```sql
... RIGHT JOIN ...    -- 5 matched + (NULL, Civil) = 6 rows
... FULL OUTER JOIN ...  -- 5 + Divya + Civil = 7 rows
```

### 5.5 Self join

"Each employee with their manager's name":

```sql
SELECT e.name AS emp, m.name AS manager
FROM Emp e JOIN Emp m ON e.mgr = m.eid;
```

| emp | manager |
|---|---|
| Aman | Riya |
| Neha | Riya |
| Karan | Neha |
| Divya | Neha |
| Arjun | Riya |

5 rows (Riya has no manager; use LEFT JOIN to include her with NULL).

---

## 6. Subqueries

A **subquery** is a SELECT inside another statement.

### 6.1 Scalar subquery (returns one value)

Highest-paid employee:
```sql
SELECT name FROM Emp WHERE salary = (SELECT MAX(salary) FROM Emp);   -- Riya
```

**Second-highest salary:**
```sql
SELECT MAX(salary) FROM Emp
WHERE salary < (SELECT MAX(salary) FROM Emp);                        -- 60000
```

### 6.2 IN / NOT IN

```sql
SELECT name FROM Emp WHERE dept IN (SELECT dept FROM Dept);          -- 5 (all but Divya)
SELECT name FROM Emp WHERE dept NOT IN (SELECT dept FROM Dept);      -- Divya
```

### 6.3 The NOT IN + NULL trap (very important)

"Employees who are not anyone's manager":

```sql
SELECT name FROM Emp WHERE eid NOT IN (SELECT mgr FROM Emp);
```

The subquery returns {NULL, 1, 1, 3, 3, 1}, which **contains a NULL**.

`eid NOT IN (NULL, 1, 3)` means `eid <> NULL AND eid <> 1 AND eid <> 3`. The first part is **UNKNOWN** for every row, so the whole condition is never TRUE.

**Result: zero rows!** Even though Aman, Karan, Divya and Arjun manage nobody.

Fixes:
```sql
... WHERE eid NOT IN (SELECT mgr FROM Emp WHERE mgr IS NOT NULL);   -- Aman, Karan, Divya, Arjun
... WHERE NOT EXISTS (SELECT * FROM Emp m WHERE m.mgr = e.eid);    -- same 4 (alias outer as e)
```

### 6.4 ANY / SOME and ALL

- `x > ANY (S)`: true if x is greater than **at least one** value in S (i.e. > the minimum).
- `x > ALL (S)`: true if x is greater than **every** value in S (i.e. > the maximum).
- `= ANY` is the same as `IN`. `<> ALL` is the same as `NOT IN`.

CS salaries = {70000, 50000, 50000}.

```sql
SELECT name FROM Emp WHERE salary > ANY (SELECT salary FROM Emp WHERE dept='CS');
-- > 50000: Riya, Neha  (2 rows)

SELECT name FROM Emp WHERE salary > ALL (SELECT salary FROM Emp WHERE dept='CS');
-- > 70000: nobody
```

Edge cases: `> ALL` of an **empty** set is **TRUE** for every row. `> ANY` of an empty set is **FALSE**.

### 6.5 EXISTS / NOT EXISTS

`EXISTS (subquery)` is TRUE if the subquery returns **at least one row**. It never yields UNKNOWN, which is why NOT EXISTS is safe with NULLs.

Departments that have at least one employee:
```sql
SELECT dname FROM Dept d
WHERE EXISTS (SELECT * FROM Emp e WHERE e.dept = d.dept);    -- Computer, Electrical
```

### 6.6 Correlated vs non-correlated

- **Non-correlated (simple):** the inner query doesn't refer to the outer query. It can run **once**.
- **Correlated:** the inner query refers to a column of the outer query, so it's (conceptually) **re-evaluated for each outer row**. Slower, but more expressive.

Employees earning more than their **own** department's average:
```sql
SELECT e.name FROM Emp e
WHERE e.salary > (SELECT AVG(salary) FROM Emp WHERE dept = e.dept);
```
- CS average 56666.67 → Riya (70000) ✓, Aman, Arjun ✗.
- EE average 60000 → Neha (60000) not greater ✗; Karan NULL ✗.
- ME average 40000 → Divya ✗.

**Result: Riya.**

### 6.7 Nth highest salary (correlated counting)

```sql
SELECT DISTINCT salary FROM Emp e1
WHERE N - 1 = (SELECT COUNT(DISTINCT salary) FROM Emp e2 WHERE e2.salary > e1.salary);
```
For N = 2: the salary with exactly one distinct salary above it = **60000**.

### 6.8 Division in SQL ("for all")

Students who took **all** courses:
```sql
SELECT s.sid FROM Student s
WHERE NOT EXISTS (
    SELECT c.cid FROM Course c
    WHERE NOT EXISTS (
        SELECT * FROM Enrolled e WHERE e.sid = s.sid AND e.cid = c.cid));
```
Read it as: "there is no course that this student has not taken". Double negation = "for all".

---

## 7. Views

A **view** is a **virtual table** defined by a stored query. It holds **no data of its own** (unless it's a **materialized view**); each time you query it, the underlying query runs on the current base tables.

```sql
CREATE VIEW cs_staff AS
SELECT eid, name, salary FROM Emp WHERE dept = 'CS';

SELECT * FROM cs_staff WHERE salary > 55000;    -- Riya
```

Uses: **simplify** complex queries, **security** (expose only some columns/rows), logical data independence.

### Updatable views

A view is generally **not updatable** if its query has:
- aggregate functions, GROUP BY, HAVING,
- DISTINCT,
- joins of multiple tables (in many systems),
- set operations,
- derived/computed columns being updated.

Reason: the DBMS can't map the change back to a unique base-table row.

`WITH CHECK OPTION`: inserts/updates through the view must satisfy the view's WHERE condition.

### Materialized views

Store the result physically, refreshed periodically or on change. Fast reads; stale data possible. Common in data warehouses.

---

## 8. PL/SQL, procedures, functions and triggers (briefly)

PL/SQL = SQL + procedural constructs (variables, IF, loops, exceptions). Block structure:

```sql
DECLARE
    v_total NUMBER;
BEGIN
    SELECT SUM(salary) INTO v_total FROM Emp;
    DBMS_OUTPUT.PUT_LINE(v_total);
EXCEPTION
    WHEN NO_DATA_FOUND THEN ...
END;
```

| | Returns a value? | How invoked |
|---|---|---|
| **Procedure** | No (may have OUT parameters) | Called explicitly (`EXEC p;` / `CALL p();`) |
| **Function** | **Yes**, exactly one | Called explicitly, can be used inside SELECT |
| **Trigger** | No | **Fires automatically** on INSERT/UPDATE/DELETE |

Triggers: **BEFORE** or **AFTER** the event; **row-level** (FOR EACH ROW, once per affected row) or **statement-level** (once per statement). Used for auditing, enforcing complex rules, maintaining derived data.

**Cursors**: a pointer to iterate over a query's result row by row (implicit for single-row statements, explicit for multi-row).

---

## 9. Exam traps

1. COUNT(\*) counts rows; COUNT(col) skips NULLs; other aggregates ignore NULLs.
2. AVG ≠ SUM/COUNT(\*) when NULLs exist.
3. Non-aggregated SELECT columns must be in GROUP BY.
4. WHERE filters rows before grouping; HAVING filters groups after.
5. `%` = zero or more; `_` = exactly one.
6. UNION removes duplicates; UNION ALL keeps them.
7. NATURAL JOIN uses **all** same-named columns.
8. **NOT IN with a NULL in the subquery returns no rows.** Use NOT EXISTS.
9. `> ALL` (empty) is TRUE; `> ANY` (empty) is FALSE.
10. Correlated subqueries are re-evaluated per outer row.
11. Views with aggregates/DISTINCT/GROUP BY aren't updatable.

---

## 10. Practice questions (use the tables above)

**Q1.** `SELECT COUNT(*), COUNT(salary), AVG(salary) FROM Emp WHERE dept = 'EE';`
(a) 2, 2, 30000 (b) 2, 1, 60000 (c) 1, 1, 60000 (d) 2, 1, 30000

**Answer: (b).**

---

**Q2.** `SELECT dept FROM Emp GROUP BY dept HAVING AVG(salary) > 50000;`
(a) CS only (b) CS, EE (c) EE only (d) CS, EE, ME

**Answer: (b).** CS 56666.67, EE 60000, ME 40000.

---

**Q3.** `SELECT name FROM Emp WHERE name LIKE '_r%';`
(a) Arjun (b) Arjun, Karan (c) Riya (d) none

**Answer: (a).** Second letter 'r': A**r**jun. (Karan's second letter is 'a'.)

---

**Q4.** Rows returned by `SELECT * FROM Emp e FULL OUTER JOIN Dept d ON e.dept = d.dept;`
(a) 5 (b) 6 (c) 7 (d) 18

**Answer: (c).**

---

**Q5.** `SELECT name FROM Emp WHERE eid NOT IN (SELECT mgr FROM Emp);` returns:
(a) Aman, Karan, Divya, Arjun (b) Riya, Neha (c) no rows (d) all six

**Answer: (c).** The subquery contains NULL.

---

**Q6.** `SELECT COUNT(*) FROM Emp e WHERE NOT EXISTS (SELECT * FROM Emp m WHERE m.mgr = e.eid);`
(a) 2 (b) 4 (c) 0 (d) 6

**Answer: (b).** Aman, Karan, Divya, Arjun manage no one.

---

**Q7.** `SELECT name FROM Emp WHERE salary >= ALL (SELECT salary FROM Emp WHERE salary IS NOT NULL);`
(a) Riya (b) no rows (c) Riya, Neha (d) all

**Answer: (a).** ≥ every salary means equal to the maximum.

---

**Q8.** What does this return?
```sql
SELECT dept, COUNT(*) FROM Emp WHERE salary > 45000 GROUP BY dept;
```
(a) (CS,3), (EE,1) (b) (CS,3), (EE,2), (ME,1) (c) (CS,3), (EE,1), (ME,0) (d) (CS,2), (EE,1)

**Answer: (a).** After WHERE: Riya, Aman, Neha, Arjun. ME has no remaining rows, so no ME group appears at all (not a group with count 0).

---

**Q9.** `(SELECT dept FROM Emp) UNION ALL (SELECT dept FROM Dept)` returns how many rows?
(a) 4 (b) 6 (c) 9 (d) 3

**Answer: (c).**

---

**Q10.** Which view is updatable in standard SQL?
(a) `SELECT dept, COUNT(*) FROM Emp GROUP BY dept` (b) `SELECT DISTINCT dept FROM Emp` (c) `SELECT eid, name FROM Emp WHERE dept = 'CS'` (d) `SELECT e.name, d.dname FROM Emp e JOIN Dept d ON e.dept = d.dept`

**Answer: (c).** Single table, no aggregates, no DISTINCT.

---

**Q11.** `SELECT AVG(salary) FROM Emp;` versus `SELECT SUM(salary)/COUNT(*) FROM Emp;`:
(a) both 54000 (b) 54000 and 45000 (c) 45000 and 54000 (d) both 45000

**Answer: (b).**

---

**Q12.** In the correlated query of section 6.6, how many times (conceptually) is the inner query evaluated?
(a) once (b) 3 times (one per department) (c) 6 times (one per Emp row) (d) 18 times

**Answer: (c).**

---

**Q13.** `SELECT MAX(salary) FROM Emp WHERE dept = 'XY';` (no such dept) returns:
(a) 0 (b) NULL (c) no rows (d) error

**Answer: (b).** An aggregate without GROUP BY always returns exactly one row; MAX of an empty set is NULL. (COUNT would return 0.)

---

**Q14.** Which keyword makes a trigger run once for every affected row?
(a) FOR EACH STATEMENT (b) FOR EACH ROW (c) BEFORE (d) INSTEAD OF

**Answer: (b).**

---

**Q15.** `x <> ALL (subquery)` is equivalent to:
(a) x IN (subquery) (b) x NOT IN (subquery) (c) x = ANY (subquery) (d) EXISTS (subquery)

**Answer: (b).**

---

**Practice questions:** [6.10 SQL Aggregates Joins Subqueries Views](../ISRO_CS_Question_Bank/06_DBMS/6.10_SQL_Aggregates_Joins_Subqueries_Views.md)
