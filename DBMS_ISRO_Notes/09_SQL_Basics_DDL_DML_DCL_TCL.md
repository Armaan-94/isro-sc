# 09. SQL Basics: SELECT, WHERE, DDL, DML, DCL, TCL

> **How to learn SQL for an exam.** Don't memorise syntax in a vacuum. Keep **one small table** in your head and run every query against it mentally. We'll use the same table throughout this chapter and the next, so you can predict outputs precisely.

---

## 1. What SQL is

**SQL (Structured Query Language)** is the standard language for relational databases.

- Started as **SEQUEL** at **IBM** (System R project, 1970s).
- Standardised by ANSI/ISO as **SQL-86**, then SQL-92, SQL:1999, ..., SQL:2023.
- **Declarative**: you say **what** you want; the DBMS decides **how** (the optimizer picks the plan).
- Based on relational algebra and tuple relational calculus, but uses **bag (multiset)** semantics: **duplicates are kept** unless you ask otherwise.

### Sub-languages

| Group | Commands | Purpose |
|---|---|---|
| **DDL** | CREATE, ALTER, DROP, TRUNCATE, RENAME | Define structure |
| **DML** | SELECT, INSERT, UPDATE, DELETE | Work with data |
| **DCL** | GRANT, REVOKE | Permissions |
| **TCL** | COMMIT, ROLLBACK, SAVEPOINT | Transactions |

(SELECT is sometimes separated as **DQL**.)

---

## 2. Our running example

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

(Note: Divya's dept ME is not in Dept, and CE has no employees. We'll use that for outer joins.)

---

## 3. DDL: creating and changing structure

### CREATE TABLE

```sql
CREATE TABLE Emp (
    eid     INT PRIMARY KEY,
    name    VARCHAR(50) NOT NULL,
    dept    CHAR(2),
    salary  DECIMAL(10,2) CHECK (salary >= 0),
    mgr     INT,
    email   VARCHAR(100) UNIQUE,
    joined  DATE DEFAULT CURRENT_DATE,
    FOREIGN KEY (mgr) REFERENCES Emp(eid) ON DELETE SET NULL
);
```

Constraints: `PRIMARY KEY`, `NOT NULL`, `UNIQUE`, `CHECK`, `DEFAULT`, `FOREIGN KEY ... REFERENCES`.

- `PRIMARY KEY` = UNIQUE + NOT NULL, only one per table.
- `UNIQUE` columns **may contain NULL** (in most systems, multiple NULLs are allowed, since NULL ≠ NULL).

### Data types

| Category | Types | Notes |
|---|---|---|
| Exact numeric | INT, SMALLINT, DECIMAL(p, s) / NUMERIC | DECIMAL is **exact**: use for money. DECIMAL(10,2) = 10 digits total, 2 after the point |
| Approximate numeric | FLOAT, REAL, DOUBLE | Binary floating point: rounding errors |
| Character | **CHAR(n)**: fixed length, padded with spaces. **VARCHAR(n)**: variable length up to n. TEXT: long | CHAR(10) storing 'Cat' uses 10 characters |
| Date/time | DATE, TIME, TIMESTAMP, INTERVAL | DATE = 'YYYY-MM-DD' |
| Boolean | BOOLEAN | TRUE/FALSE/UNKNOWN |
| Binary | BLOB | Images, files |

### ALTER TABLE

```sql
ALTER TABLE Emp ADD phone VARCHAR(15);           -- add column
ALTER TABLE Emp DROP COLUMN phone;                -- remove column
ALTER TABLE Emp MODIFY name VARCHAR(100);         -- change type (Oracle/MySQL syntax)
ALTER TABLE Emp RENAME COLUMN salary TO pay;      -- rename column
ALTER TABLE Emp ADD CONSTRAINT chk CHECK (pay > 0);
ALTER TABLE Emp DROP CONSTRAINT chk;
```

### DROP, TRUNCATE, DELETE (the classic comparison)

| | DELETE | TRUNCATE | DROP |
|---|---|---|---|
| Category | **DML** | **DDL** | **DDL** |
| Removes | Selected rows (WHERE allowed) | **All** rows | Rows **and** the table structure |
| Structure kept? | Yes | Yes | **No** |
| Rollback? | **Yes** (before commit) | Generally **no** (auto-commit) | Generally **no** |
| Fires row triggers? | Yes | No | No |
| Speed | Slow (row by row, logged) | Fast (deallocates pages) | Fast |

`DROP TABLE Emp CASCADE` also drops dependent objects (views, FK constraints). `RESTRICT` refuses if dependents exist.

---

## 4. DML: INSERT, UPDATE, DELETE

```sql
INSERT INTO Emp VALUES (7, 'Isha', 'CS', 55000, 1);
INSERT INTO Emp (eid, name) VALUES (8, 'Om');        -- other columns get NULL/default
INSERT INTO EmpBackup SELECT * FROM Emp WHERE dept = 'CS';   -- insert from a query

UPDATE Emp SET salary = salary * 1.10 WHERE dept = 'CS';
UPDATE Emp SET salary = 0;                             -- no WHERE: ALL rows!

DELETE FROM Emp WHERE salary < 45000;
DELETE FROM Emp;                                       -- all rows, table remains
```

> **Trap.** Forgetting WHERE in UPDATE/DELETE affects **every** row.

---

## 5. The SELECT statement

### 5.1 Structure

```sql
SELECT   [DISTINCT] column-list      -- 5. choose columns
FROM     tables                       -- 1. get the data
WHERE    row-condition                -- 2. filter rows
GROUP BY columns                      -- 3. form groups
HAVING   group-condition              -- 4. filter groups
ORDER BY columns [ASC|DESC]           -- 6. sort
LIMIT n                               -- 7. cut (MySQL/PostgreSQL)
```

**Logical execution order: FROM → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT.**

Consequences:
- A column **alias** defined in SELECT **can't be used in WHERE** (WHERE runs first), but **can** be used in ORDER BY.
- Aggregates can't appear in WHERE (groups don't exist yet).

Only **SELECT** and **FROM** are mandatory (some systems even allow SELECT without FROM).

### 5.2 SELECT is projection, WHERE is selection

- `SELECT name, dept` ↔ Π_name,dept (but **without** removing duplicates).
- `WHERE dept = 'CS'` ↔ σ_dept='CS'.

```sql
SELECT dept FROM Emp;              -- CS, CS, EE, EE, ME, CS   (6 rows, duplicates kept)
SELECT DISTINCT dept FROM Emp;     -- CS, EE, ME               (3 rows)
```

`DISTINCT` applies to the **whole selected row**, not one column:

```sql
SELECT DISTINCT dept, salary FROM Emp;
-- (CS,70000) (CS,50000) (EE,60000) (EE,NULL) (ME,40000)   -> 5 rows (Aman and Arjun collapse)
```

### 5.3 Expressions and aliases

```sql
SELECT name, salary * 12 AS annual FROM Emp;
```

The computed column is just **displayed**; stored data is unchanged. `AS` aliases are temporary.

### 5.4 Operators in WHERE

| Operator | Example | Note |
|---|---|---|
| `= <> != < > <= >=` | `salary <> 50000` | `<>` is the standard "not equal" |
| `AND OR NOT` | | Precedence: NOT > AND > OR |
| `BETWEEN a AND b` | `salary BETWEEN 50000 AND 60000` | **Inclusive** both ends: Aman, Neha, Arjun |
| `IN (list)` | `dept IN ('CS','ME')` | Membership |
| `LIKE` | `name LIKE 'A%'` | Pattern (next chapter) |
| `IS NULL / IS NOT NULL` | `salary IS NULL` | **Only** way to test for NULL |

**Precedence trap:**
```sql
WHERE dept = 'CS' OR dept = 'EE' AND salary > 55000
```
AND binds tighter: `dept='CS' OR (dept='EE' AND salary>55000)` → Riya, Aman, Arjun (CS) + Neha (EE, 60000) = 4 rows. Use parentheses to be explicit.

### 5.5 ORDER BY

```sql
SELECT name, salary FROM Emp ORDER BY salary DESC, name ASC;
```

Default is ASC. NULLs sort first or last depending on the DBMS (Oracle and PostgreSQL treat NULL as larger than everything by default; MySQL and SQL Server treat it as smaller).

---

## 6. NULL: the source of many traps

NULL means **unknown / missing / not applicable**. It is **not** 0, not '', not a space.

### 6.1 Three-valued logic

Any comparison with NULL gives **UNKNOWN**, not TRUE or FALSE. Even `NULL = NULL` is UNKNOWN.

| AND | TRUE | UNKNOWN | FALSE |
|---|---|---|---|
| **TRUE** | TRUE | UNKNOWN | FALSE |
| **UNKNOWN** | UNKNOWN | UNKNOWN | FALSE |
| **FALSE** | FALSE | FALSE | FALSE |

| OR | TRUE | UNKNOWN | FALSE |
|---|---|---|---|
| **TRUE** | TRUE | TRUE | TRUE |
| **UNKNOWN** | TRUE | UNKNOWN | UNKNOWN |
| **FALSE** | TRUE | UNKNOWN | FALSE |

NOT UNKNOWN = UNKNOWN.

**WHERE keeps a row only if the condition is TRUE.** UNKNOWN rows are dropped, just like FALSE.

### 6.2 Consequences on our table

- `WHERE salary = NULL` → **no rows** (always UNKNOWN). Use `IS NULL` → Karan.
- `WHERE salary > 45000` → Riya, Aman, Neha, Arjun (4). Karan is excluded (UNKNOWN).
- `WHERE NOT (salary > 45000)` → Divya only (1). Karan is excluded **again**! So the two queries together don't cover all 6 rows.
- `WHERE salary > 45000 OR dept = 'EE'` → Karan: UNKNOWN OR TRUE = TRUE → included. Total: Riya, Aman, Neha, Karan, Arjun = **5**.
- Arithmetic with NULL gives NULL: `salary + 1000` for Karan is NULL.

---

## 7. DCL: GRANT and REVOKE

```sql
GRANT SELECT, UPDATE ON Emp TO analyst;
GRANT SELECT ON Emp TO analyst WITH GRANT OPTION;   -- analyst may grant it to others
REVOKE UPDATE ON Emp FROM analyst;
REVOKE SELECT ON Emp FROM analyst CASCADE;          -- also revoke from people analyst granted to
```

Privileges: SELECT, INSERT, UPDATE, DELETE, REFERENCES, ALL PRIVILEGES. Roles group privileges: `CREATE ROLE`, `GRANT role TO user`.

---

## 8. TCL: COMMIT, ROLLBACK, SAVEPOINT

```sql
UPDATE Emp SET salary = salary + 5000 WHERE eid = 2;
SAVEPOINT s1;
DELETE FROM Emp WHERE eid = 5;
ROLLBACK TO s1;        -- undoes the DELETE only; the UPDATE is still pending
COMMIT;                -- makes the UPDATE permanent
```

- **COMMIT:** make all changes since the last commit permanent.
- **ROLLBACK:** undo all changes since the last commit.
- **SAVEPOINT name:** mark a point; `ROLLBACK TO name` undoes only what came after it.
- DDL statements usually perform an **implicit COMMIT** (Oracle, MySQL), so a ROLLBACK after `CREATE TABLE` won't undo earlier DML either.

---

## 9. Seeing the schema

| Command | Shows |
|---|---|
| `DESC Emp;` / `DESCRIBE Emp;` | Columns, types, nullability, keys |
| `SHOW TABLES;` (MySQL) | Table names (Oracle: `SELECT table_name FROM user_tables;`) |
| `SELECT * FROM Emp;` | The data |

---

## 10. Exam traps

1. Execution order: **FROM, WHERE, GROUP BY, HAVING, SELECT, DISTINCT, ORDER BY**.
2. SQL keeps duplicates; use DISTINCT. DISTINCT applies to the whole row.
3. BETWEEN is **inclusive**.
4. `= NULL` never matches; use `IS NULL`.
5. WHERE keeps only TRUE rows; UNKNOWN is dropped.
6. AND has higher precedence than OR.
7. DELETE (DML, rollback, WHERE) vs TRUNCATE (DDL, all rows, structure stays) vs DROP (DDL, structure gone).
8. CHAR is fixed-length; VARCHAR variable. DECIMAL is exact.
9. UNIQUE allows NULLs; PRIMARY KEY doesn't.
10. Aliases from SELECT can't be used in WHERE.

---

## 11. Practice questions (use the Emp table)

**Q1.** `SELECT COUNT(*) FROM Emp WHERE salary BETWEEN 50000 AND 70000;`
(a) 2 (b) 3 (c) 4 (d) 5

**Answer: (c).** Riya 70000, Aman 50000, Neha 60000, Arjun 50000. Inclusive bounds.

---

**Q2.** `SELECT name FROM Emp WHERE salary = NULL;` returns:
(a) Karan (b) no rows (c) all rows (d) an error in every DBMS

**Answer: (b).**

---

**Q3.** `SELECT DISTINCT salary FROM Emp;` returns how many rows?
(a) 4 (b) 5 (c) 6 (d) 3

**Answer: (b).** 70000, 50000, 60000, NULL, 40000. DISTINCT treats NULLs as one group, so NULL appears once.

---

**Q4.** `SELECT name FROM Emp WHERE dept = 'EE' OR dept = 'ME' AND salary > 50000;` returns:
(a) Neha only (b) Neha, Karan (c) Neha, Karan, Divya (d) Neha, Divya

**Answer: (b).** AND first: (dept='ME' AND salary>50000) is false for Divya. So the condition reduces to dept = 'EE': Neha, Karan.

---

**Q5.** Which statement removes all rows but keeps the table, and generally can't be rolled back?
(a) DELETE (b) TRUNCATE (c) DROP (d) ALTER

**Answer: (b).**

---

**Q6.** `SELECT name, salary*2 AS dbl FROM Emp WHERE dbl > 100000;` will:
(a) work, returning Riya and Neha (b) fail, because the alias dbl isn't available in WHERE (c) return all rows (d) return Karan

**Answer: (b).** WHERE is evaluated before SELECT. (Writing `WHERE salary*2 > 100000` works.)

---

**Q7.** How many rows does `SELECT name FROM Emp WHERE NOT (salary >= 50000);` return?
(a) 1 (b) 2 (c) 3 (d) 0

**Answer: (a).** Divya only. Karan's comparison is UNKNOWN, and NOT UNKNOWN is UNKNOWN, so he's excluded.

---

**Q8.** After `UPDATE Emp SET salary = salary + 1000;`, Karan's salary is:
(a) 1000 (b) NULL (c) 0 (d) error

**Answer: (b).** NULL + 1000 = NULL.

---

**Q9.**
```sql
INSERT INTO Emp VALUES (9, 'Zara', 'CS', 45000, 1);
SAVEPOINT a;
DELETE FROM Emp WHERE eid = 9;
ROLLBACK TO a;
COMMIT;
```
Is Zara in the table at the end?
(a) Yes (b) No

**Answer: (a).** The rollback undoes only the delete.

---

**Q10.** Which constraint allows NULL values?
(a) PRIMARY KEY (b) UNIQUE (c) NOT NULL (d) none

**Answer: (b).**

---

**Q11.** In `DECIMAL(7,2)`, the largest storable value is:
(a) 9999999.99 (b) 99999.99 (c) 9999.999 (d) 999999.99

**Answer: (b).** 7 digits total, 2 after the decimal point → 5 before.

---

**Q12.** `GRANT SELECT ON Emp TO u1 WITH GRANT OPTION;` allows u1 to:
(a) modify Emp (b) read Emp and grant SELECT to others (c) drop Emp (d) only read Emp

**Answer: (b).**

---

**Q13.** Which is the correct logical order?
(a) SELECT, FROM, WHERE, GROUP BY (b) FROM, WHERE, GROUP BY, HAVING, SELECT, ORDER BY (c) FROM, SELECT, WHERE, ORDER BY (d) WHERE, FROM, SELECT

**Answer: (b).**

---

**Q14.** `SELECT name FROM Emp WHERE mgr IN (1, 3);` returns how many names?
(a) 3 (b) 4 (c) 5 (d) 6

**Answer: (c).** mgr 1: Aman, Neha, Arjun. mgr 3: Karan, Divya. Riya's mgr is NULL.
