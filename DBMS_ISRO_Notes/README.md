# DBMS: Deep, Beginner-Friendly Notes

Read in order: each chapter uses skills from the ones before it (especially closures and keys from 03 and 04, which drive 05 and 06). Every chapter has worked examples traced by hand and practice questions with explanations.

| # | Chapter | You'll be able to |
|---|---|---|
| 01 | [DBMS Basics, Architecture, Languages](01_DBMS_Basics_Architecture_Languages.md) | Explain why DBMS exists, 3-schema architecture, data independence, DDL/DML/DCL/TCL |
| 02 | [ER Modeling](02_ER_Modeling.md) | Read ER diagrams, convert to tables, count minimum tables |
| 03 | [Relational Model and FDs](03_Relational_Model_and_Functional_Dependencies.md) | Compute attribute closures, use Armstrong's axioms, find minimal covers |
| 04 | [Keys and Integrity Constraints](04_Keys_and_Integrity_Constraints.md) | Find all candidate keys, count super keys, trace cascades |
| 05 | [Normalization 1NF to BCNF](05_Normalization_1NF_to_BCNF.md) | Determine the highest normal form of any relation |
| 06 | [Decomposition, MVD, 4NF, 5NF](06_Decomposition_MVD_4NF_5NF.md) | Test lossless join and dependency preservation |
| 07 | [File Organization, Indexing, B-Trees](07_File_Organization_Indexing_BTrees.md) | Count block accesses, compute B/B+ tree order, insert into B-trees |
| 08 | [Relational Algebra](08_Relational_Algebra.md) | Evaluate RA expressions, result sizes, division |
| 09 | [SQL Basics](09_SQL_Basics_DDL_DML_DCL_TCL.md) | Predict SELECT/WHERE outputs, NULL logic, DELETE vs TRUNCATE vs DROP |
| 10 | [SQL Aggregates, Joins, Subqueries, Views](10_SQL_Aggregates_Joins_Subqueries_Views.md) | Handle GROUP BY/HAVING, NOT IN with NULL, correlated subqueries |
| 11 | [Tuple and Domain Relational Calculus](11_Tuple_and_Domain_Relational_Calculus.md) | Read and write TRC/DRC, spot unsafe expressions |
| 12 | [Transactions, ACID, Serializability](12_Transactions_ACID_Serializability.md) | Count schedules, test conflict and view serializability |
| 13 | [Recoverability and Concurrency Control](13_Recoverability_and_Concurrency_Control.md) | Classify schedules, apply 2PL variants, timestamp ordering, wait-die/wound-wait |
| 14 | [Database Recovery](14_Database_Recovery.md) | Apply WAL, undo/redo rules, checkpoints, ARIES |
| 15 | [Warehousing, Mining, Distributed, NoSQL](15_Data_Warehousing_Mining_Distributed_NoSQL.md) | OLAP operations, Apriori support/confidence, CAP, NoSQL types |

**Highest-yield chapters for ISRO:** 04, 05, 07, 10, 12, 13. If short on time, do those first.
