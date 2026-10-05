# 02. Entity-Relationship (ER) Modeling

> **Where ER fits.** Before you build a house, you draw a blueprint. The ER model is the blueprint of a database. It's drawn **before** any tables exist, in a language that both clients and engineers can understand. Then we convert it to tables using fixed rules (section 7), and those rules are a favourite exam topic.

---

## 1. The ER model in one paragraph

Proposed by **Peter Chen in 1976**. It describes the world using three ideas:

1. **Entities**: the "things" (a student, a course, a satellite).
2. **Attributes**: properties of things (a student's name, roll number).
3. **Relationships**: associations between things (a student **enrolls in** a course).

It's a **conceptual (high-level)** design tool.

---

## 2. Entities and entity sets

- **Entity:** a real-world object distinguishable from others. Can be **concrete** (a person, a book) or **abstract** (a course, a loan, a flight booking).
- **Entity set (entity type):** a collection of similar entities sharing the same attributes, e.g. all students. Drawn as a **rectangle**. Becomes a **table**.
- An individual entity becomes a **row** of that table.

An ER diagram shows entity **sets** (schema), not individual entities (instances). You'll never see "Riya" drawn in an ER diagram.

---

## 3. Attributes (types you must recognise)

| Type | Meaning | Example | ER symbol |
|---|---|---|---|
| **Simple (atomic)** | Can't be divided | Age, Roll no. | Ellipse |
| **Composite** | Made of sub-parts | Name = First + Middle + Last; Address = Street + City + PIN | Ellipse with child ellipses |
| **Single-valued** | One value per entity | Aadhaar number | Ellipse |
| **Multivalued** | Several values possible | Phone numbers, email addresses, skills | **Double ellipse** |
| **Stored** | Physically stored | Date of birth | Ellipse |
| **Derived** | Computed from others | Age (from DOB), total marks | **Dashed ellipse** |
| **Key** | Uniquely identifies an entity | Roll no. | **Underlined** name |
| **Descriptive** | Belongs to a **relationship**, not to an entity | `date_enrolled` on Student–Enrolls–Course | Ellipse attached to the diamond |

Why does `date_enrolled` belong to the relationship? Because it isn't a property of the student alone (a student enrols in many courses on different dates) or of the course alone. It describes the **pairing**.

### NULL values

A NULL can mean:
- **Not applicable**: e.g. "apartment number" for someone living in a house.
- **Unknown**: the value exists but isn't recorded (missing), or we don't know if it exists.

---

## 4. Relationships

A **relationship** is an association among entities, drawn as a **diamond**. A **relationship set** is a collection of similar relationships.

### Degree (how many entity sets take part)

| Degree | Entity sets | Example |
|---|---|---|
| **Unary (recursive)** | 1 | Employee **supervises** Employee |
| **Binary** | 2 | Student **enrolls** Course (most common) |
| **Ternary** | 3 | Supplier **supplies** Part **to** Project |
| **n-ary** | n | |

In unary relationships, **roles** distinguish the two sides (supervisor vs subordinate).

---

## 5. Constraints on relationships

### 5.1 Cardinality ratio (mapping cardinality)

How many entities on one side can be associated with how many on the other.

| Ratio | Meaning | Example |
|---|---|---|
| **1 : 1** | Each A with at most one B, each B with at most one A | Person **has** Passport |
| **1 : N** | Each A with many B, each B with at most one A | Department **has** Employees |
| **N : 1** | Mirror image of 1 : N | Employees **work in** Department |
| **M : N** | Many to many | Students **enroll in** Courses |

### 5.2 Participation constraint

Does **every** entity have to take part in the relationship?

- **Total participation:** every entity in the set participates. **Minimum cardinality ≥ 1.** Drawn with a **double line**. Example: every Loan must be associated with some Customer.
- **Partial participation:** some entities may not participate. **Minimum cardinality = 0.** Single line. Example: not every Customer has a Loan.

> **Trap.** Total participation is about the **minimum** (≥ 1), not the maximum. "Total participation means maximum cardinality 1" is wrong.

### 5.3 (min, max) notation

Some books write `(min, max)` on each line: `(0, N)`, `(1, 1)`, etc. Min = 1 → total participation. Max = 1 → "one" side.

---

## 6. Strong and weak entity sets

### Strong entity set

Has its **own primary key**. Example: `Employee(emp_id, name)`.

### Weak entity set

Does **not** have enough attributes to form a primary key on its own. It **depends** on an **owner (identifying) entity**.

Example: an insurance policy covers an employee's **dependents**. A dependent is identified by name only **within** one employee's family (two employees could both have a child named "Aarav").

- Weak entity set: **double rectangle**.
- **Identifying relationship**: **double diamond**.
- **Partial key (discriminator)**: attribute that distinguishes weak entities **of the same owner**. Drawn with a **dashed underline**.

Rules:
- The identifying relationship is **many-to-one** from the weak entity to the owner (one employee, many dependents).
- The weak entity's participation is **always total** (a dependent can't exist without an employee).
- **Primary key of weak entity = primary key of owner + partial key.**
  `Dependent(emp_id, dep_name, age)` with PK `(emp_id, dep_name)`.

Why model it this way? It reflects real dependence: delete the employee, and their dependents' records should go too (**cascading delete**).

---

## 7. Converting ER to tables (the rules)

These rules decide how many tables you need. Learn them like multiplication tables.

| ER construct | Relational result |
|---|---|
| **Strong entity set** | Its own table; PK = its key |
| **Weak entity set** | Its own table; PK = owner's PK + partial key; owner's PK is also an FK |
| **Composite attribute** | One column per **simple** component (no new table) |
| **Multivalued attribute** | **New table**: (owner's PK, attribute), both together as PK |
| **Derived attribute** | Usually **not stored** (computed when needed) |
| **Binary 1 : 1** | No new table. Put the PK of one side as an FK in the other. **Prefer the side with total participation** (avoids NULLs) |
| **Binary 1 : N** | No new table. Put the PK of the **"1" side** as an FK in the **"N" side** |
| **Binary M : N** | **New table**; PK = PKs of both sides combined (both are FKs) + any descriptive attributes |
| **Unary relationship** | 1:1 or 1:N: add an FK column referencing the same table. M:N: new table |
| **Ternary / n-ary** | **New table**; PK typically = combination of participating PKs |

> **Most tested rule.** Only **M : N** and **ternary or higher** relationships (and multivalued attributes) need their own table. 1:1 and 1:N relationships are absorbed into existing tables via a foreign key.

### Why 1:N goes on the N side

`Department (1) —— has —— (N) Employee`. If you put the FK in Department, one department row would need **many** employee IDs (a multivalued column, not allowed). Put `dept_id` in Employee instead: each employee row has exactly one department. Easy.

### Why M:N needs a new table

`Student (M) —— enrolls —— (N) Course`. Neither side can hold a single FK, because each student has many courses **and** each course has many students. So create `Enrolls(roll_no, course_id, date_enrolled)` with PK `(roll_no, course_id)`.

### Merging 1:1 with total participation on both sides

If both sides of a 1:1 relationship have **total** participation, the two entity sets can be merged into **one** table (every row on each side has exactly one partner).

### Minimum number of tables: worked examples

**Example 1:** Entities A, B, C (all strong). A –(1:N)– B, B –(M:N)– C.
- Tables: A, B, C, plus one for the M:N = **4**.

**Example 2:** Entities E1 and E2 with a **1:1** relationship, E1 total participation, E2 partial.
- Put E2's PK as FK in E1. Tables: **2**.

**Example 3:** Entities E1 and E2, **1:1**, **both total**.
- Merge into **1** table.

**Example 4:** Entity Student has a multivalued attribute Phone and a composite attribute Name. Student –(M:N)– Course. Course –(N:1)– Department.
- Student, Student_Phone, Course, Department, Enrolls = **5**.

**Example 5:** Weak entity Dependent with owner Employee, Employee –(N:1)– Department.
- Employee, Dependent, Department = **3** (identifying relationship is absorbed; dept FK goes in Employee).

---

## 8. Extended ER features (EER)

- **Specialization (top-down):** split an entity set into sub-classes. `Employee` → `Engineer`, `Manager`. Sub-classes inherit attributes. Shown with an "ISA" triangle.
- **Generalization (bottom-up):** combine similar entity sets into a super-class. `Car`, `Truck` → `Vehicle`.
- Constraints: **disjoint vs overlapping** (can an entity belong to more than one sub-class?), **total vs partial** (must every super-class entity belong to some sub-class?).
- **Aggregation:** treat a relationship as an entity so it can participate in another relationship. E.g. (Employee works-on Project) is **monitored by** Manager.

---

## 9. ER design traps: fan trap and chasm trap

### Fan trap

Two or more **1:N relationships fan out from the same entity**, making it impossible to tell which instances on one "many" side go with which on the other.

```
Department <-(N:1)- Site -(1:N)-> Staff
```

A site has many departments and many staff. Which staff work in which department? **Can't tell.** Fix: restructure, e.g. Site –(1:N)– Department –(1:N)– Staff.

### Chasm trap

A path between two entities exists only through a third entity with **partial participation**, so for some occurrences the path is **missing**.

```
Branch -(1:N)- Staff -(1:N, partial)- Property
```

Some properties are not handled by any staff member, so you can't find which branch they belong to. Fix: add a **direct** Branch–Property relationship.

| | Fan trap | Chasm trap |
|---|---|---|
| Cause | 1:N relationships fanning out from one entity | Partial participation on an indirect path |
| Symptom | **Ambiguous** associations | **Missing** associations |
| Fix | Restructure the relationships | Add a direct relationship |

---

## 10. Pros and cons of ER diagrams

**Advantages:** simple, intuitive, good for communication with non-technical users, maps directly onto tables.

**Disadvantages:** can't express every constraint (e.g. "salary ≤ manager's salary"), no standard notation (Chen, crow's foot, UML all differ), no data manipulation, cumbersome for big systems.

---

## 11. Exam traps

1. **Double ellipse = multivalued**; **dashed ellipse = derived**; **double rectangle = weak entity**; **double diamond = identifying relationship**; **double line = total participation**.
2. Total participation = **min ≥ 1**.
3. Weak entity PK = owner PK + partial key.
4. Multivalued attribute → **separate table**.
5. Only M:N, n-ary relationships and multivalued attributes create **new** tables.
6. 1:N → FK on the **N** side.
7. 1:1 with both total → can be **one** table.
8. Fan trap = ambiguous; chasm trap = missing path.

---

## 12. Practice questions

**Q1.** In an ER diagram, a double ellipse denotes:
(a) derived attribute (b) multivalued attribute (c) key attribute (d) composite attribute

**Answer: (b).**

---

**Q2.** A weak entity set always has:
(a) partial participation in the identifying relationship (b) total participation in the identifying relationship (c) its own primary key (d) a 1:1 identifying relationship

**Answer: (b).**

---

**Q3.** Entities: Student, Course, Faculty (all strong). Student –(M:N)– Course; Course –(N:1)– Faculty. Minimum number of tables?
(a) 3 (b) 4 (c) 5 (d) 6

**Answer: (b).** 3 entities + 1 M:N table. The N:1 is absorbed (faculty_id goes into Course).

---

**Q4.** Two entity sets with a 1:1 relationship, total participation on both sides. Minimum number of tables?
(a) 1 (b) 2 (c) 3 (d) 4

**Answer: (a).**

---

**Q5.** Entity Employee has a multivalued attribute "skills". Converting to tables gives:
(a) one table with a comma-separated skills column (b) Employee table and Employee_Skills(emp_id, skill) with composite PK (c) one table per skill (d) skills is dropped

**Answer: (b).**

---

**Q6.** Age, computed from Date of Birth, is a:
(a) stored attribute (b) derived attribute (c) composite attribute (d) multivalued attribute

**Answer: (b).**

---

**Q7.** In a 1:N relationship between Department (1) and Employee (N), the foreign key goes in:
(a) Department (b) Employee (c) a new table (d) both

**Answer: (b).**

---

**Q8.** "Employee supervises Employee" is a relationship of degree:
(a) 0 (b) 1 (c) 2 (d) 3

**Answer: (b).** Unary (recursive): one entity set.

---

**Q9.** A ternary relationship among A, B, C (all strong) needs how many tables in total (minimum)?
(a) 3 (b) 4 (c) 6 (d) 1

**Answer: (b).** A, B, C, plus the relationship table.

---

**Q10.** The attribute that distinguishes weak entities belonging to the same owner is called:
(a) primary key (b) foreign key (c) discriminator / partial key (d) super key

**Answer: (c).**

---

**Q11.** An attribute "date_of_joining" placed on the relationship "works_in" between Employee and Project is a:
(a) composite attribute (b) descriptive attribute (c) derived attribute (d) key attribute

**Answer: (b).**

---

**Q12.** Which situation describes a chasm trap?
(a) two 1:N relationships fanning from one entity (b) a path between two entities passing through an entity with partial participation (c) an M:N relationship (d) a weak entity without an owner

**Answer: (b).**

---

**Q13.** Converting a 1:1 relationship where Manager (partial) manages Department (total). Where should the FK go to avoid NULLs?
(a) Manager table, referencing Department (b) Department table, referencing Manager (c) New table (d) Either, no difference

**Answer: (b).** Every department has a manager (total), so `manager_id` in Department is never NULL. Putting `dept_id` in Manager would leave NULLs for managers who manage nothing.

---

**Q14.** Specialization is a:
(a) bottom-up process (b) top-down process (c) normalization step (d) type of key

**Answer: (b).**

---

**Q15.** Entities A (key a), B (key b). Relationship R is M:N with a descriptive attribute d. Schema of R's table?
(a) R(a, d) (b) R(a, b, d) with PK (a, b) (c) R(b, d) (d) R(a, b, d) with PK (a, b, d)

**Answer: (b).**

---

**Practice questions:** [6.02 ER Modeling](../ISRO_CS_Question_Bank/06_DBMS/6.02_ER_Modeling.md)
