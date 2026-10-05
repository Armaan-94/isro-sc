# 01. DBMS Basics: Architecture and Languages

> **Start here.** Before any SQL or normalization, you need to understand *why* databases exist and how they're organised. Every later chapter is a refinement of the ideas in this one.

---

## 1. Data, information, database, DBMS

- **Data:** raw facts. `92`, `"Riya"`, `2026-10-04`. Meaningless on their own.
- **Information:** data **processed and placed in context**. "Riya scored 92 in DBMS on 4 Oct 2026."
- **Database:** an organised collection of related data, stored electronically, so it can be accessed and managed easily.
- **DBMS (Database Management System):** the **software** that lets you **define, create, query, update, and control access** to a database. Examples: MySQL, PostgreSQL, Oracle, SQL Server, SQLite.

**Database system = Database + DBMS + application programs.**

---

## 2. Why not just use files? (The problems DBMS solves)

Imagine a college that stores data in plain files: `students.txt` for the admissions office, `hostel.txt` for the hostel office, `fees.txt` for accounts. Each office writes its own programs. Here's what goes wrong:

| Problem | What happens | Example |
|---|---|---|
| **Data redundancy and inconsistency** | Same fact stored in several files; update one, forget another | Riya's phone number changed in `students.txt` but not in `hostel.txt` |
| **Difficulty accessing data** | No general query facility; every new question needs a new program | "List students from Pune with fees pending" means writing a new C program |
| **Data isolation** | Data scattered in different files with different formats | Hard to combine `fees.txt` (CSV) with `hostel.txt` (fixed-width) |
| **Integrity problems** | Rules (constraints) buried in program code, hard to enforce and change | "Marks between 0 and 100" coded into 10 different programs |
| **Atomicity problems** | A crash mid-operation leaves data half-updated | ₹5000 deducted from account A, crash, never added to account B |
| **Concurrent-access anomalies** | Two users update simultaneously and overwrite each other | Two clerks book the last hostel room at the same moment |
| **Security problems** | Hard to give each user access to only part of the data | Accounts clerk can read everyone's medical records |

> **Trap.** Atomicity and concurrency are **different** problems. Atomicity = crash mid-operation. Concurrency = simultaneous users. Don't merge them in "list the problems" questions.

A DBMS fixes all of these by putting data in **one central, controlled place** with a **query language**, **constraints**, **transactions**, **concurrency control**, and **access control**.

---

## 3. Three-schema architecture (levels of abstraction)

Different people need different views of the same database. The **ANSI/SPARC three-schema architecture** separates them:

```
        View 1     View 2     View 3        <- EXTERNAL / VIEW level (end users)
            \        |        /
             \       |       /
          +----------------------+
          |  CONCEPTUAL / LOGICAL |          <- what data, which relationships (designers)
          +----------------------+
                     |
          +----------------------+
          |  INTERNAL / PHYSICAL  |          <- how it's stored on disk (DBA)
          +----------------------+
```

| Level | Describes | Who works here | Example |
|---|---|---|---|
| **Physical / internal** | **How** data is stored: files, indexes, block layouts, compression | DBA, DBMS itself | "Student table stored as a B+ tree clustered on roll_no" |
| **Logical / conceptual** | **What** data exists and the relationships, types, constraints | Database designers | `Student(roll_no, name, dept_id)`, `Dept(dept_id, name)` |
| **View / external** | The **part** of the database a particular user sees | End users, application programs | The library app sees only `roll_no, name` |

### Data independence (the whole point of the levels)

**Data independence** = the ability to change the schema at one level **without** changing the level above.

- **Physical data independence:** change the physical schema (add an index, move to a new disk, change file organisation) **without changing the logical schema** or applications.
- **Logical data independence:** change the logical schema (add a column, split a table) **without changing external views** / application programs.

> **Trap.** **Physical data independence is easier to achieve** than logical. Applications depend heavily on the logical structure (table and column names), but hardly at all on how bytes sit on disk.

Example: adding an index on `name` to speed up searches is **physical** data independence.

---

## 4. Schema vs instance

| Schema | Instance |
|---|---|
| The **design / structure** of the database | The **actual data** at a particular moment |
| Defined once, changes rarely | Changes constantly |
| Like a variable's **declared type** | Like the variable's **current value** |
| `Student(roll_no INT, name VARCHAR(50))` | `{(1, "Riya"), (2, "Aman")}` right now |

A schema is also called the **intension**; an instance, the **extension** or **database state**.

---

## 5. Who's who: users and the DBA

- **DBA (Database Administrator):** central control.
  - Schema definition.
  - Storage structure and access-method definition.
  - Schema and physical-organisation modification.
  - **Granting authorization** for data access.
  - Routine maintenance: **backups**, monitoring disk space and performance, upgrades.
- **Database designers:** decide the logical structure.
- **Application programmers:** write programs that use the database.
- **End users:** casual (ad-hoc queries), naive (use forms/apps, e.g. a bank teller), sophisticated (analysts writing complex queries).

---

## 6. OLTP vs OLAP

Two very different kinds of database workload.

| Feature | OLTP (Online Transaction Processing) | OLAP (Online Analytical Processing) |
|---|---|---|
| Purpose | Run the **day-to-day** business | **Analyse** data for decisions |
| Typical query | Short, simple: "debit ₹500 from account 123" | Complex, long: "sales trend by region over 5 years" |
| Data | **Current**, detailed | **Historical**, summarised, consolidated (data warehouse) |
| Size | MB to GB | TB to PB |
| Operations | Many small inserts/updates/deletes | Mostly reads; periodic bulk loads |
| Design | **Normalised** (many small tables, avoid redundancy) | **Denormalised** (star schema, fewer big tables, fast reads) |
| Users | Clerks, customers, apps (thousands) | Analysts, managers (few) |
| Example | Railway ticket booking, ATM | Business intelligence dashboards |

---

## 7. Data models

A **data model** is a way of describing data, relationships, meaning and constraints.

| Model | Structure | Notes |
|---|---|---|
| **Hierarchical** | **Tree**: each child has **exactly one parent** | Rigid; many-to-many is awkward. IBM IMS |
| **Network** | **Graph**: a child can have **multiple parents** | More flexible than hierarchical; complex pointers. CODASYL |
| **Relational** | **Tables** (relations) of rows (tuples) and columns (attributes) | **Most widely used**. Proposed by **E. F. Codd (1970)** |
| **Entity-Relationship** | Entities, attributes, relationships (diagrams) | Used for **design**, not storage ([Chapter 02](02_ER_Modeling.md)) |
| **Object-oriented** | Objects with attributes and methods | Complex data: CAD, multimedia |
| **Object-relational** | Relational + object features | PostgreSQL, Oracle |
| **NoSQL / semi-structured** | Documents, key-value, column-family, graph | [Chapter 15](15_Data_Warehousing_Mining_Distributed_NoSQL.md) |

> **Trap.** The network model **generalises** the hierarchical model (allowing multiple parents). They're related, not unrelated.

---

## 8. DBMS languages

| Category | Purpose | Commands |
|---|---|---|
| **DDL** (Data Definition Language) | Define/modify the **schema** | `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME` |
| **DML** (Data Manipulation Language) | Work with the **data** | `SELECT`, `INSERT`, `UPDATE`, `DELETE` |
| **DCL** (Data Control Language) | **Permissions** | `GRANT`, `REVOKE` |
| **TCL** (Transaction Control Language) | Manage **transactions** | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

Some books call `SELECT` alone **DQL** (Data Query Language).

### Procedural vs non-procedural DML

- **Procedural (low-level):** you specify **what** data you want **and how** to get it, record by record. Usually embedded in a host language like C or Java. Example: relational algebra (in spirit), navigating records in old network databases.
- **Non-procedural / declarative (high-level):** you specify only **what** you want; the DBMS figures out how. **SQL** is declarative. So is relational calculus.

### Two important facts

- **TRUNCATE is DDL**, not DML. It removes all rows quickly (deallocates pages), usually **can't be rolled back**, and doesn't fire row triggers. `DELETE` (DML) removes rows one by one and **can** be rolled back.
- **DDL statements usually cause an implicit COMMIT** in most RDBMSs (e.g. Oracle, MySQL). So `CREATE TABLE` in the middle of a transaction commits what came before it. (PostgreSQL is an exception: its DDL is transactional.)

### Ways programs talk to a DBMS

- Stand-alone query tools (SQL*Plus, `psql`, MySQL Workbench).
- **Embedded SQL** inside a host language (e.g. SQL in C with a precompiler; SQLJ for Java).
- **APIs**: **JDBC** (Java), **ODBC** (C and others).
- **Database programming languages**: PL/SQL (Oracle), T-SQL (SQL Server).
- Scripting languages (Python, PHP) for web apps.
- Menu/form-based and graphical interfaces for naive users.

---

## 9. Database architectures

### Centralized

DBMS, data, application programs, and user interface processing all on **one** machine. Remote terminals only display output.

### Client-server: 2-tier

```
[Client: UI + application logic]  <---->  [Database server]
```

- The **application (business) logic lives on the client**.
- Simple, but every client needs the logic installed; less secure (clients talk directly to the DB); scales poorly.

### Client-server: 3-tier

```
[Client: UI only]  <---->  [Application server: business logic]  <---->  [Database server]
```

- **Presentation, logic and data** are separated.
- **Standard for web apps**: browser (client), web/app server, database.
- Better **security** (clients never touch the DB directly), **scalability** and **maintainability**.

> **Trap.** 2-tier: business logic on the **client**. 3-tier: business logic in the **middle tier** (application server).

---

## 10. DBMS components (internal structure, briefly)

- **Query processor:** DDL interpreter, DML compiler, **query optimizer** (chooses the cheapest execution plan), query evaluation engine.
- **Storage manager:** authorization and integrity manager, **transaction manager** (ensures ACID), file manager, **buffer manager** (decides what to cache in RAM).
- **Disk storage:** data files, **data dictionary** (metadata: the "data about data", i.e. schemas, constraints, users), indexes, statistics.

---

## 11. Exam traps

1. Six/seven problems of file systems: redundancy & inconsistency, access difficulty, isolation, integrity, **atomicity**, **concurrency**, security.
2. Physical data independence (easier) vs logical (harder).
3. Schema = structure, instance = current data.
4. `GRANT/REVOKE` = **DCL**. `COMMIT/ROLLBACK` = **TCL**. `TRUNCATE` = **DDL**.
5. DDL causes an implicit commit in most systems.
6. SQL is **non-procedural / declarative**.
7. Network model allows multiple parents; hierarchical only one.
8. 3-tier puts logic in the middle tier.
9. OLAP = historical, denormalised, complex reads. OLTP = current, normalised, short transactions.

---

## 12. Practice questions

**Q1.** Which level of the three-schema architecture describes how data is actually stored?
(a) External (b) Conceptual (c) Physical (d) View

**Answer: (c).**

---

**Q2.** Adding a new column to a table without having to change existing application programs illustrates:
(a) physical data independence (b) logical data independence (c) data redundancy (d) data isolation

**Answer: (b).** The logical (conceptual) schema changed; external views/apps didn't need to.

---

**Q3.** Which is a DDL command?
(a) DELETE (b) UPDATE (c) TRUNCATE (d) GRANT

**Answer: (c).**

---

**Q4.** Which statement about TRUNCATE vs DELETE is correct?
(a) Both are DML (b) TRUNCATE can have a WHERE clause (c) DELETE can be rolled back; TRUNCATE generally cannot (d) TRUNCATE fires row-level triggers

**Answer: (c).**

---

**Q5.** The data dictionary stores:
(a) user data (b) metadata (data about data) (c) only indexes (d) log records

**Answer: (b).**

---

**Q6.** Which data model was proposed by E. F. Codd?
(a) Hierarchical (b) Network (c) Relational (d) Object-oriented

**Answer: (c).**

---

**Q7.** Which property of a DBMS directly addresses the "half-updated data after a crash" problem?
(a) Security (b) Atomicity (c) Isolation from file formats (d) Redundancy

**Answer: (b).**

---

**Q8.** In a 3-tier architecture, a browser is the:
(a) database tier (b) application tier (c) presentation (client) tier (d) storage tier

**Answer: (c).**

---

**Q9.** The overall design of a database is called its:
(a) instance (b) schema (c) state (d) snapshot

**Answer: (b).**

---

**Q10.** Which language category does SAVEPOINT belong to?
(a) DDL (b) DML (c) DCL (d) TCL

**Answer: (d).**

---

**Q11.** OLAP systems are typically:
(a) highly normalised with short transactions (b) denormalised with complex read-heavy queries on historical data (c) used for ATM withdrawals (d) updated continuously by thousands of users

**Answer: (b).**

---

**Q12.** Which of these is easier to achieve?
(a) Logical data independence (b) Physical data independence (c) Both equally (d) Neither is possible

**Answer: (b).**

---

**Q13.** In the hierarchical model, a child record can have:
(a) exactly one parent (b) any number of parents (c) no parent (d) exactly two parents

**Answer: (a).**

---

**Q14.** "SQL specifies what data to retrieve, not how to retrieve it." This makes SQL:
(a) procedural (b) non-procedural / declarative (c) object-oriented (d) low-level

**Answer: (b).**

---

**Q15.** Who is responsible for granting authorization to database users?
(a) Naive user (b) Application programmer (c) DBA (d) Query optimizer

**Answer: (c).**

---

**Practice questions:** [6.01 DBMS Basics Architecture Languages](../ISRO_CS_Question_Bank/06_DBMS/6.01_DBMS_Basics_Architecture_Languages.md)
