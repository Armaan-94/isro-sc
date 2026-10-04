# 15. Data Warehousing, Data Mining, Distributed Databases and NoSQL

> **Beyond the classic RDBMS.** Organisations don't just record transactions; they **analyse** years of them (data warehousing, OLAP, mining), **spread** data across sites (distributed databases), and handle web-scale data that doesn't fit neat tables (NoSQL). Exam questions here are mostly definitions and a few numericals (support/confidence).

---

## Part A: Data warehousing

### 1. Definition (W. H. Inmon)

A **data warehouse** is a **subject-oriented, integrated, time-variant, non-volatile** collection of data that supports management's **decision-making**.

| Property | Meaning |
|---|---|
| **Subject-oriented** | Organised around business subjects (customer, product, sales), not around daily operations |
| **Integrated** | Combines data from many heterogeneous sources with consistent names, units and codes |
| **Time-variant** | Keeps **history** (5 to 10 years), every record tagged with time |
| **Non-volatile** | Data is **loaded and read**, not updated in place. No need for transaction-level concurrency control or recovery |

> **Trap.** A data warehouse is **non-volatile**. "Volatile" is a classic wrong option.

### 2. ETL

How data gets in:
1. **Extract** from source systems (OLTP databases, files, external feeds).
2. **Transform**: clean (fix errors, missing values), convert units, deduplicate, integrate, aggregate.
3. **Load** into the warehouse (usually in periodic batches).

### 3. Architecture

- **Bottom tier:** the warehouse database server (fed by ETL).
- **Middle tier:** **OLAP server** (ROLAP: relational; MOLAP: multidimensional arrays; HOLAP: hybrid).
- **Top tier:** front-end tools: query, reporting, dashboards, data mining.

A **data mart** is a smaller, department-specific warehouse (e.g. just Sales).

### 4. Schemas

- **Fact table:** the central table of **measures** (numbers to analyse: units sold, revenue) plus **foreign keys** to dimensions. Very large.
- **Dimension tables:** descriptive context (time, product, store, customer). Smaller.

| Schema | Shape | Dimensions | Joins | Notes |
|---|---|---|---|---|
| **Star** | One fact table, dimensions around it | **Denormalised** | Fewer, faster | Simplest, most common |
| **Snowflake** | Star whose dimensions are split into sub-tables | **Normalised** | More | Less redundancy, slower queries |
| **Fact constellation (galaxy)** | Several fact tables sharing dimensions | | | For complex enterprises |

```
STAR:                         SNOWFLAKE:
   Time     Product              Time     Product -- Category
      \     /                       \     /
       Sales (fact)                  Sales (fact)
      /     \                       /     \
   Store   Customer              Store -- City -- State
```

> **Trap.** **Snowflake = normalised dimensions.** Star = denormalised.

### 5. Data cube and OLAP operations

A **data cube** views measures across several dimensions (e.g. sales by **time** × **product** × **region**).

| Operation | What it does | Example |
|---|---|---|
| **Roll-up** (drill-up) | Summarise: climb a concept hierarchy or drop a dimension | city → state → country; month → year |
| **Drill-down** | Opposite: more detail | year → quarter → month |
| **Slice** | Fix **one** dimension to one value → a 2-D sub-cube | Sales for **Q1** only |
| **Dice** | Select on **two or more** dimensions → a smaller sub-cube | Q1 and Q2, for Mumbai and Delhi, for phones |
| **Pivot (rotate)** | Swap axes for a different view | Products as rows instead of columns |

---

## Part B: Data mining

### 6. KDD process

**Data mining** is **one step** in **Knowledge Discovery in Databases (KDD)**:

1. Data cleaning
2. Data integration
3. Data selection
4. Data transformation
5. **Data mining** (apply algorithms to find patterns)
6. Pattern evaluation
7. Knowledge presentation

Preprocessing tasks: **cleaning, integration, transformation (normalisation, aggregation), reduction** (sampling, dimensionality reduction).

### 7. Mining tasks

| Task | Labels? | Idea | Examples |
|---|---|---|---|
| **Classification** | **Predefined** classes (supervised) | Learn from labelled data, predict class of new data | Decision trees, Naive Bayes, SVM, k-NN |
| **Prediction / regression** | Supervised | Predict a numeric value | Linear regression |
| **Clustering** | **No** labels (unsupervised) | Group by similarity: high intra-cluster, low inter-cluster similarity | k-means, k-medoids, hierarchical, DBSCAN |
| **Association rules** | No labels | Items that occur together | Apriori, FP-Growth |
| **Outlier analysis** | | Find unusual objects | Fraud detection |
| **Evolution analysis** | | Trends over time | Stock trends |

> **Trap.** Classification = **supervised** (needs labels). Clustering = **unsupervised**.

Clustering families: **partitioning** (k-means: must choose k in advance), **hierarchical** (agglomerative/divisive, gives a dendrogram, no k needed), **density-based** (DBSCAN, finds arbitrary shapes, handles noise), grid-based, model-based.

### 8. Association rules and Apriori

**Support** of itemset X = fraction of transactions containing X.
**Confidence** of rule X → Y = support(X ∪ Y) / support(X). ("Of the baskets with X, what fraction also have Y?")

A rule is **strong** if support ≥ min_support **and** confidence ≥ min_confidence.

**Apriori property (downward closure / anti-monotone):** **every subset of a frequent itemset is frequent.** Equivalently, if an itemset is infrequent, all its supersets are infrequent → **prune** them without counting.

**Algorithm:** find frequent 1-itemsets (L1); join Lk with itself to make candidate (k+1)-itemsets; prune candidates having an infrequent k-subset; scan the database to count; keep those meeting min support; repeat until empty.

#### Worked example

| TID | Items |
|---|---|
| T1 | Bread, Milk |
| T2 | Bread, Diaper, Beer, Eggs |
| T3 | Milk, Diaper, Beer, Coke |
| T4 | Bread, Milk, Diaper, Beer |
| T5 | Bread, Milk, Diaper, Coke |

min_support = 3 transactions (60%).

**1-itemsets:** Bread 4, Milk 4, Diaper 4, Beer 3, Coke 2, Eggs 1.
L1 = {Bread, Milk, Diaper, Beer}.

**2-itemsets (candidates from L1):**

| Pair | Count |
|---|---|
| Bread, Milk | 3 (T1, T4, T5) ✓ |
| Bread, Diaper | 3 (T2, T4, T5) ✓ |
| Bread, Beer | 2 ✗ |
| Milk, Diaper | 3 (T3, T4, T5) ✓ |
| Milk, Beer | 2 ✗ |
| Diaper, Beer | 3 (T2, T3, T4) ✓ |

**3-itemset candidates:** joining gives {Bread, Milk, Diaper}, {Bread, Diaper, Beer}, {Milk, Diaper, Beer}.
- {Bread, Diaper, Beer}: subset {Bread, Beer} is infrequent → **pruned** (no counting needed).
- {Milk, Diaper, Beer}: subset {Milk, Beer} infrequent → **pruned**.
- {Bread, Milk, Diaper}: all subsets frequent → count: T4, T5 = 2 ✗.

L3 = ∅. Done.

**Rules:**
- Diaper → Beer: support 3/5 = 60%, confidence 3/4 = **75%**.
- Beer → Diaper: support 60%, confidence 3/3 = **100%**.

Note that confidence isn't symmetric.

**FP-Growth** finds frequent itemsets **without candidate generation**, using a compressed FP-tree. **ECLAT** uses a vertical (item → TID list) format.

---

## Part C: Distributed databases

### 9. Basics

A **distributed database** is stored across **multiple sites** connected by a network, managed so that it appears as one database. Sites share **no physical memory or disk**.

- **Homogeneous:** same DBMS software and schema everywhere; sites cooperate fully.
- **Heterogeneous:** different DBMSs/schemas; harder to query and transact across.

### 10. Fragmentation and replication

**Horizontal fragmentation:** split a relation by **rows** (selection σ). Each fragment has **all columns** for a subset of rows. Reconstruct with **union**.
Example: customers of North region at site 1, South region at site 2.

**Vertical fragmentation:** split by **columns** (projection Π). Each fragment must **include the primary key** so the pieces can be rejoined losslessly. Reconstruct with **natural join**.
Example: (emp_id, name, dept) at HR site; (emp_id, salary, bank_acc) at Payroll site.

**Mixed (hybrid):** both.

**Replication:** keep **copies** at several sites.
- + faster local reads, availability, fault tolerance.
- − updates must reach every copy (consistency overhead).
- **Full replication:** every site has the whole database.

### 11. Transparency

Users shouldn't need to know:
- **Fragmentation transparency:** how a relation is split.
- **Replication transparency:** how many copies exist and where.
- **Location transparency:** where data is stored.

### 12. Distributed transactions

Atomicity across sites uses **Two-Phase Commit (2PC)** (see OS chapter 13): prepare/vote, then commit/abort. It's **blocking** if the coordinator fails after votes. **3PC** reduces blocking.

Distributed query processing tries to minimise **data transfer** across the network, e.g. using **semi-joins** (send only the join column, filter remotely, send back matching rows).

---

## Part D: NoSQL

### 13. Why NoSQL?

Web-scale applications have **Big Data**: huge **Volume**, high **Velocity**, wide **Variety** (structured, semi-structured, unstructured). Rigid schemas and single-server RDBMSs struggle. **NoSQL ("Not Only SQL")** databases:
- are **schema-flexible**,
- **scale horizontally** (add more commodity machines),
- often relax strict ACID in favour of **BASE**.

### 14. BASE vs ACID

| ACID (RDBMS) | BASE (many NoSQL) |
|---|---|
| Atomic, Consistent, Isolated, Durable | **B**asically **A**vailable, **S**oft state, **E**ventual consistency |
| Strong consistency | Replicas may disagree briefly; they **converge** eventually |

### 15. CAP theorem (Brewer)

A distributed data store can guarantee at most **two** of:
- **C**onsistency: every read sees the latest write.
- **A**vailability: every request gets a (non-error) response.
- **P**artition tolerance: the system keeps working despite network partitions.

Since partitions **will** happen in any real distributed system, **P is mandatory**, and the real choice during a partition is **C vs A**:
- **CP** systems: refuse some requests to stay consistent (HBase, MongoDB with default settings).
- **AP** systems: always answer, possibly with stale data (Cassandra, DynamoDB, CouchDB).

### 16. The four NoSQL families

| Type | Data model | Good for | Examples |
|---|---|---|---|
| **Key-value** | A giant hash map: key → opaque value | Caching, sessions, simple lookups | **Redis**, DynamoDB, Riak, Memcached |
| **Document** | Key → structured document (JSON/BSON/XML), queryable by fields | Content, catalogs, flexible records | **MongoDB**, CouchDB |
| **Column-family (wide-column)** | Rows with dynamic columns grouped into families; stored by column | Huge write-heavy datasets, analytics, time series | **Cassandra**, **HBase**, Bigtable |
| **Graph** | Nodes and edges with properties | Relationship-heavy queries: social networks, recommendations, routing | **Neo4j**, OrientDB, JanusGraph |

Columnar storage keeps each column's values together, so **aggregates** like SUM(sales) read only that column: fast analytics.

---

## 17. Exam traps

1. Warehouse: subject-oriented, integrated, time-variant, **non-volatile**.
2. Star = denormalised; snowflake = normalised dimensions.
3. Roll-up ↔ drill-down; slice = one dimension fixed; dice = several.
4. Data mining is **one step** of KDD.
5. Classification supervised, clustering unsupervised.
6. Confidence(X → Y) = support(X ∪ Y)/support(X).
7. Apriori: subsets of frequent itemsets are frequent; prune supersets of infrequent ones.
8. Horizontal = rows (union to rebuild); vertical = columns + key (join to rebuild).
9. CAP: with P unavoidable, choose C or A.
10. Graph DB for relationships; key-value for caching; document for JSON; column-family for big analytics.

---

## 18. Practice questions

**Q1.** Which is NOT a characteristic of a data warehouse?
(a) Subject-oriented (b) Integrated (c) Volatile (d) Time-variant

**Answer: (c).**

---

**Q2.** Viewing yearly sales after viewing monthly sales is:
(a) drill-down (b) roll-up (c) slice (d) pivot

**Answer: (b).**

---

**Q3.** Selecting sales for product = 'Phone' only (one dimension fixed) is a:
(a) dice (b) slice (c) roll-up (d) pivot

**Answer: (b).**

---

**Q4.** In 1000 transactions, 200 contain {milk}, and 150 contain {milk, bread}. Support of {milk, bread} and confidence of milk → bread?
(a) 15%, 75% (b) 20%, 75% (c) 15%, 15% (d) 75%, 15%

**Answer: (a).** Support = 150/1000. Confidence = 150/200.

---

**Q5.** Using the Apriori example table: confidence of Milk → Bread?
(a) 60% (b) 75% (c) 100% (d) 50%

**Answer: (b).** support(Milk, Bread) = 3, support(Milk) = 4 → 3/4.

---

**Q6.** If {A, B} is infrequent, then {A, B, C}:
(a) must be frequent (b) must be infrequent (c) may be frequent (d) has the same support

**Answer: (b).** Anti-monotone property.

---

**Q7.** k-means clustering is an example of:
(a) supervised learning (b) hierarchical clustering (c) partitioning clustering (d) association mining

**Answer: (c).**

---

**Q8.** Splitting an Employee table so that salary columns are at one site and personal columns at another (both with emp_id) is:
(a) horizontal fragmentation (b) vertical fragmentation (c) replication (d) snowflaking

**Answer: (b).**

---

**Q9.** BASE stands for:
(a) Basic Atomic Serial Execution (b) Basically Available, Soft state, Eventually consistent (c) Balanced, Available, Scalable, Efficient (d) Batch And Stream Engine

**Answer: (b).**

---

**Q10.** According to CAP, during a network partition a system must choose between:
(a) C and P (b) A and P (c) C and A (d) nothing; all three are possible

**Answer: (c).**

---

**Q11.** Which NoSQL database is a graph database?
(a) Redis (b) MongoDB (c) Cassandra (d) Neo4j

**Answer: (d).**

---

**Q12.** A schema with one fact table and denormalised dimension tables is a:
(a) snowflake schema (b) star schema (c) galaxy schema (d) 3NF schema

**Answer: (b).**

---

**Q13.** In the KDD process, which step comes immediately before data mining?
(a) Pattern evaluation (b) Data transformation (c) Knowledge presentation (d) Data cleaning

**Answer: (b).**

---

**Q14.** Horizontal fragments are recombined using:
(a) natural join (b) union (c) Cartesian product (d) division

**Answer: (b).**
