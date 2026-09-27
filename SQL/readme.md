# SQL & DBMS Interview Preparation

> For a developer who **can write queries** but wants to be strong on **DBMS theory, the "why", and performance** — the parts interviewers drill on. No CS degree assumed; every concept is explained from the ground up, then taken to interview depth.

**How to use this**
- The theory sections (1, 2, 7) are where non-degree devs usually lose marks — read those carefully and rehearse the spoken answers.
- Query sections show **the same problem written multiple ways** (subquery → join → window function) and then **optimized**, because that's exactly how interviews escalate.
- Examples use standard SQL; PostgreSQL/MySQL differences are called out where they matter.

## Table of Contents
1. [DBMS Fundamentals (Theory)](#1-dbms-fundamentals-theory)
2. [Keys, Constraints & Normalization](#2-keys-constraints--normalization)
3. [Joins, Sets, Grouping & NULLs](#3-joins-sets-grouping--nulls)
4. [Query Types + Subquery ↔ Join ↔ Window Rewrites & Optimization](#4-query-types--rewrites--optimization)
5. [Indexing & Internals](#5-indexing--internals)
6. [Query Optimization, EXPLAIN & Performance](#6-query-optimization-explain--performance)
7. [Transactions, ACID, Isolation, Locking & Deadlocks](#7-transactions-acid-isolation-locking--deadlocks)
8. [Advanced SQL (Window Functions, CTEs, Views, Procedures)](#8-advanced-sql)
9. [Interview Query Problem Bank](#9-interview-query-problem-bank)
10. [Theory Q&A with Counter-Questions](#10-theory-qa-with-counter-questions)
11. [Last-Minute Revision Checklist](#last-minute-revision-checklist)

**Sample schema used throughout:**
```sql
employees(emp_id PK, name, dept_id FK, manager_id FK->emp_id, salary, hire_date, city)
departments(dept_id PK, dept_name, location)
orders(order_id PK, customer_id FK, order_date, amount, status)
customers(customer_id PK, name, email, city, created_at)
```

---

# 1. DBMS Fundamentals (Theory)

## 1.1 What is a DBMS? DBMS vs RDBMS
- **DBMS (Database Management System)** — software to store, retrieve, and manage data with controlled access, concurrency, and integrity (e.g., file-based, hierarchical, network, relational).
- **RDBMS (Relational DBMS)** — a DBMS that stores data as **relations (tables)** of **tuples (rows)** and **attributes (columns)**, with relationships enforced via keys, and queried with SQL (MySQL, PostgreSQL, Oracle, SQL Server).

**DBMS vs RDBMS (interview answer):** Every RDBMS is a DBMS, but not vice versa. An RDBMS specifically organizes data into related tables, enforces relationships/constraints (PK/FK), guarantees ACID, and supports SQL. A plain DBMS may just store data (even in files) without the relational model or constraints.

**DBMS vs File System:** A file system stores raw data with no relationships, high redundancy, no concurrency control, no query language, and weak integrity/security. A DBMS adds structured querying, reduced redundancy, concurrency control, integrity constraints, transactions, and access control.

## 1.2 Core terminology
- **Relation** = table. **Tuple** = row. **Attribute** = column. **Degree** = number of columns. **Cardinality** = number of rows.
- **Domain** — allowed set of values for an attribute (its type + constraints).
- **Schema** — the *structure* (definition) of the DB (tables, columns, types, constraints). **Instance** — the actual *data* at a moment.
- **Metadata** — data about data (stored in the system catalog / `information_schema`).

## 1.3 Three-schema architecture (asked as "levels of abstraction")
1. **Physical (internal) level** — how data is physically stored (files, indexes, blocks).
2. **Logical (conceptual) level** — what data is stored and the relationships (tables/columns) — this is the schema you design.
3. **View (external) level** — how individual users/apps see subsets of the data (views).

**Why it matters — data independence:**
- **Logical data independence** — change the logical schema (add a column) without breaking views/apps.
- **Physical data independence** — change storage/indexing without changing the logical schema.

## 1.4 The relational model & integrity rules
- **Entity integrity** — the primary key can't be NULL (every row is uniquely identifiable).
- **Referential integrity** — a foreign key must match an existing primary key value in the referenced table, or be NULL.
- **Domain integrity** — values must be valid for the column's domain (type, CHECK constraints).

## 1.5 SQL command categories
| Category | Meaning | Commands |
|---|---|---|
| **DDL** | Data Definition (structure) | CREATE, ALTER, DROP, TRUNCATE, RENAME |
| **DML** | Data Manipulation (rows) | SELECT, INSERT, UPDATE, DELETE |
| **DCL** | Data Control (permissions) | GRANT, REVOKE |
| **TCL** | Transaction Control | COMMIT, ROLLBACK, SAVEPOINT, SET TRANSACTION |

Note: some texts put SELECT under **DQL** (Data Query Language).

### DELETE vs TRUNCATE vs DROP (very common)
| | DELETE | TRUNCATE | DROP |
|---|---|---|---|
| Type | DML | DDL | DDL |
| Removes | selected rows (WHERE) | all rows | table + structure |
| WHERE | Yes | No | No |
| Rollback | Yes (logged per row) | Usually no / limited | No |
| Triggers | Fires | Doesn't fire | N/A |
| Resets identity | No | Yes | N/A |
| Speed | Slow (row by row) | Fast (deallocates pages) | Fast |

**Interview answer:** DELETE removes rows one at a time with logging and can be filtered and rolled back. TRUNCATE quickly removes all rows by deallocating data pages, resets auto-increment, doesn't fire triggers, and is DDL (auto-commits in most engines). DROP removes the whole table including its structure.

## 1.6 Logical order of SQL execution (crucial, explains many "why" questions)
You *write* `SELECT ... FROM ... WHERE ... GROUP BY ... HAVING ... ORDER BY`, but the engine *logically evaluates* it in this order:
```text
1. FROM / JOIN      -> build the working set of rows
2. WHERE            -> filter rows (no aggregates allowed here)
3. GROUP BY         -> group rows
4. HAVING           -> filter groups (aggregates allowed)
5. SELECT           -> compute columns / expressions / aliases
6. DISTINCT
7. ORDER BY         -> sort (can use SELECT aliases)
8. LIMIT / OFFSET   -> take a slice
```
**This explains:**
- Why you **can't use a `SELECT` alias in `WHERE`** (WHERE runs before SELECT) but **can in `ORDER BY`**.
- Why **aggregates go in `HAVING`, not `WHERE`** (grouping hasn't happened yet at WHERE time).
- Why `WHERE` filters rows and `HAVING` filters groups.

## 1.7 Interview Q&A (fundamentals)

#### Q1. DBMS vs RDBMS?
**Answer:** A DBMS manages data storage and access generally; an RDBMS is a DBMS built on the relational model — data in related tables, relationships enforced by keys, ACID guarantees, and SQL as the query language. RDBMS is a specialized DBMS.

**Counter Q:** Give a non-relational DBMS example and when you'd use it.
**Answer:** Document stores (MongoDB), key-value (Redis), wide-column (Cassandra), graph (Neo4j). Use them for flexible/unstructured schemas, massive horizontal scale, or specialized access patterns where the rigid relational model or joins are a poor fit — accepting weaker cross-entity consistency.

**Counter Q:** What does ACID give you that a NoSQL store might not?
**Answer:** Strong transactional guarantees — atomic multi-row/multi-table commits, consistency via constraints, isolation between concurrent transactions, and durability. Many NoSQL stores trade some of these (often consistency) for availability and scale (CAP theorem).

#### Q2. Why is the logical execution order important?
**Answer:** Because it explains what's legal where. WHERE runs before SELECT and GROUP BY, so you can't reference SELECT aliases or aggregates in WHERE; aggregate filtering must go in HAVING after grouping; ORDER BY runs last so it can use aliases. Knowing this prevents a whole class of errors and "why doesn't this work" questions.

**Counter Q:** Can you use a column in ORDER BY that isn't in SELECT?
**Answer:** Yes in most engines for plain queries (ORDER BY sees the full FROM rows). But with `SELECT DISTINCT` or `GROUP BY`, you can generally only order by selected/grouped/aggregated expressions.

### Quick Revision
- RDBMS = relational + keys + ACID + SQL; DBMS is the general term.
- 3 levels: physical/logical/view → data independence.
- Integrity: entity (PK not null), referential (FK valid), domain (valid values).
- DDL/DML/DCL/TCL; DELETE(row,rollback) vs TRUNCATE(all,fast,reset) vs DROP(structure).
- Execution order: FROM→WHERE→GROUP BY→HAVING→SELECT→ORDER BY→LIMIT.

---

# 2. Keys, Constraints & Normalization

## 2.1 Keys (know every one — heavily asked)
- **Super key** — any set of columns that uniquely identifies a row (may have extra columns).
- **Candidate key** — a *minimal* super key (no redundant column). A table can have several.
- **Primary key** — the candidate key chosen as the main identifier. Unique + **NOT NULL**, one per table.
- **Alternate key** — candidate keys not chosen as primary.
- **Composite key** — a key made of two or more columns.
- **Foreign key** — a column referencing another table's PK/unique key; enforces referential integrity.
- **Unique key** — enforces uniqueness; allows (typically) one NULL; can have several per table.
- **Surrogate key** — an artificial key (auto-increment id / UUID) with no business meaning.
- **Natural key** — a key from real data (email, SSN).

**Primary key vs Unique key (classic):**
| | Primary key | Unique key |
|---|---|---|
| NULLs | Not allowed | One NULL allowed (engine-dependent) |
| Count per table | One | Many |
| Purpose | Row identity | Enforce uniqueness on other columns |
| Index | Usually clustered | Non-clustered |

**Surrogate vs natural key:** Surrogate (auto-id) is stable, small, join-friendly, and never changes — preferred as PK. Natural keys carry meaning but can change (email) and be large; often kept as a UNIQUE constraint alongside a surrogate PK.

## 2.2 Constraints
- **NOT NULL** — column can't be NULL.
- **UNIQUE** — no duplicate values.
- **PRIMARY KEY** — unique + not null.
- **FOREIGN KEY** — referential integrity; supports `ON DELETE/UPDATE CASCADE | SET NULL | RESTRICT | NO ACTION`.
- **CHECK** — value must satisfy a condition (`CHECK (salary > 0)`).
- **DEFAULT** — value used when none supplied.

```sql
CREATE TABLE employees (
    emp_id      BIGINT PRIMARY KEY,
    name        VARCHAR(100) NOT NULL,
    email       VARCHAR(255) UNIQUE,
    dept_id     BIGINT REFERENCES departments(dept_id) ON DELETE SET NULL,
    salary      NUMERIC(12,2) CHECK (salary > 0),
    status      VARCHAR(20) DEFAULT 'ACTIVE'
);
```

**ON DELETE options:** CASCADE (delete children too), SET NULL (null the FK), RESTRICT/NO ACTION (block the delete if children exist).

## 2.3 Functional Dependency (the foundation of normalization)
- **Functional dependency `X → Y`** — X determines Y: if two rows agree on X, they must agree on Y (e.g., `emp_id → name`).
- **Partial dependency** — a non-key attribute depends on **part** of a composite key (violates 2NF).
- **Transitive dependency** — a non-key attribute depends on another **non-key** attribute (violates 3NF).

## 2.4 Normalization (step by step with examples)
**Goal:** organize data to reduce **redundancy** and prevent **anomalies**.

### The anomalies (why we normalize)
- **Insertion anomaly** — can't add data without unrelated data (can't add a department until an employee exists).
- **Update anomaly** — same fact stored many times; update one copy and miss others → inconsistency.
- **Deletion anomaly** — deleting a row loses unrelated facts (deleting the last employee deletes the department info).

### Unnormalized → normalized

**Start (bad — repeating groups, redundancy):**
```text
emp_id | name | dept_id | dept_name | skills
1      | Ana  | 10      | Sales     | Java, SQL
```

**1NF — atomic values, no repeating groups.** Each cell holds a single value; each row unique.
```text
emp_id | name | dept_id | dept_name | skill
1      | Ana  | 10      | Sales     | Java
1      | Ana  | 10      | Sales     | SQL
```

**2NF — 1NF + no partial dependency** (every non-key attribute depends on the *whole* composite key).
If PK is (emp_id, skill), then `dept_name` depends only on `emp_id` (part of the key) → move it out.
```text
employee(emp_id PK, name, dept_id)
employee_skill(emp_id, skill)   -- PK (emp_id, skill)
```

**3NF — 2NF + no transitive dependency** (non-key attributes depend only on the key, not on other non-key attributes).
`dept_name` depends on `dept_id` (a non-key attribute) → move departments out.
```text
employee(emp_id PK, name, dept_id FK)
department(dept_id PK, dept_name)
employee_skill(emp_id, skill)
```

**BCNF (Boyce-Codd)** — a stricter 3NF: for every dependency `X → Y`, X must be a **super key**. Handles edge cases where a non-trivial dependency has a determinant that isn't a candidate key (e.g., overlapping candidate keys).

**Higher forms (brief):** **4NF** removes multi-valued dependencies; **5NF** removes join dependencies. Rarely needed in practice — most schemas target **3NF/BCNF**.

### Mnemonic
- 1NF: atomic columns.
- 2NF: whole key (no partial dependency).
- 3NF: nothing but the key (no transitive dependency).
- *"Each non-key attribute depends on the key, the whole key, and nothing but the key."*

## 2.5 Denormalization
Deliberately reintroducing redundancy (duplicated columns, precomputed aggregates) to **reduce joins and speed reads**. Trade-off: faster reads, but writes must keep duplicates in sync (risk of inconsistency). Common in reporting, analytics, and read-heavy paths (e.g., storing `order_total` instead of summing items every time).

## 2.6 ER model & relationships (quick)
- **Entity** — a thing (Employee). **Attribute** — property. **Relationship** — association (works_in).
- **Cardinality**: 1:1, 1:N, M:N.
- **M:N** relationships need a **junction/bridge table** (`student_course(student_id, course_id)`) — a relational DB can't store M:N directly.
- **Weak entity** — can't be identified without a parent (depends on an owner's key).

## 2.7 Interview Q&A

#### Q1. Explain normalization and why we do it.
**Answer:** Normalization organizes tables so each fact is stored once, removing redundancy and preventing insertion, update, and deletion anomalies. We progress through normal forms: 1NF makes values atomic, 2NF removes partial dependencies on part of a composite key, 3NF removes transitive dependencies on non-key columns, and BCNF tightens 3NF so every determinant is a super key. In practice I aim for 3NF/BCNF and then selectively denormalize for read performance.

**Counter Q:** What's the difference between 2NF and 3NF?
**Answer:** 2NF eliminates partial dependencies — a non-key attribute depending on only part of a composite key. 3NF eliminates transitive dependencies — a non-key attribute depending on another non-key attribute. 2NF is about the composite key; 3NF is about non-key columns depending on other non-key columns.

**Counter Q:** When would you denormalize?
**Answer:** For read-heavy or analytical workloads where joins are expensive and data changes rarely. I'd duplicate or precompute values to cut joins, accepting that writes now must maintain those copies (often via triggers, application logic, or scheduled jobs).

**Counter Q:** Primary key vs unique key?
**Answer:** A primary key uniquely identifies a row, can't be NULL, and there's one per table (usually clustered). A unique key also enforces uniqueness but allows one NULL and you can have several per table. PK is identity; UNIQUE is an additional uniqueness rule.

**Counter Q:** Difference between a candidate key and a super key?
**Answer:** A super key is any column set that uniquely identifies rows, possibly with extra columns. A candidate key is a minimal super key — remove any column and it's no longer unique. The primary key is one chosen candidate key.

#### Q2. Can a foreign key be NULL? Can it reference the same table?
**Answer:** Yes to both. A nullable FK means the relationship is optional (an employee with no department). A **self-referencing** FK models hierarchies (`manager_id` referencing `emp_id` in the same table).

### Quick Revision
- Super ⊃ candidate ⊃ primary; PK not null+one; UNIQUE allows a null+many.
- FD X→Y; partial dep breaks 2NF; transitive dep breaks 3NF.
- Anomalies: insert/update/delete → the reason to normalize.
- 1NF atomic, 2NF whole key, 3NF nothing but the key, BCNF determinant = super key.
- M:N needs a junction table; denormalize for read speed.

---

# 3. Joins, Sets, Grouping & NULLs

## 3.1 Join types (know all, with a mental picture)
```text
INNER JOIN      -> rows matching in BOTH tables
LEFT JOIN       -> all LEFT rows + matches (NULLs where no right match)
RIGHT JOIN      -> all RIGHT rows + matches
FULL OUTER JOIN -> all rows from both (NULLs where no match)
CROSS JOIN      -> Cartesian product (every row x every row)
SELF JOIN       -> a table joined to itself (hierarchies, comparisons)
```

```sql
-- INNER: employees that have a department
SELECT e.name, d.dept_name
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id;

-- LEFT: ALL employees, dept_name NULL if none
SELECT e.name, d.dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;

-- SELF JOIN: each employee with their manager's name
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id;
```

### Finding "rows with no match" (anti-join) — common interview task
```sql
-- Departments that have NO employees
SELECT d.dept_name
FROM departments d
LEFT JOIN employees e ON e.dept_id = d.dept_id
WHERE e.emp_id IS NULL;         -- the LEFT-JOIN-IS-NULL anti-join pattern
```

### INNER vs OUTER — the key idea
- **INNER** keeps only matched rows.
- **OUTER** (LEFT/RIGHT/FULL) keeps unmatched rows too, filling missing side with NULLs.

**Trap — filter in WHERE vs ON with LEFT JOIN:**
```sql
-- BUG: this turns the LEFT JOIN back into an INNER JOIN
SELECT e.name, d.dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id
WHERE d.location = 'NY';        -- NULL rows fail this predicate -> dropped!

-- CORRECT: put the filter in the ON clause to keep unmatched left rows
SELECT e.name, d.dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id AND d.location = 'NY';
```
**Why:** conditions in `ON` are applied *during* the join (before NULLs are produced); conditions in `WHERE` are applied *after*, and a `NULL = value` test is never true, so it removes the unmatched rows — silently converting your LEFT JOIN into an INNER JOIN.

## 3.2 Set operations
| Operator | Meaning | Duplicates |
|---|---|---|
| **UNION** | Combine + remove duplicates | removed |
| **UNION ALL** | Combine, keep everything | kept (faster) |
| **INTERSECT** | Rows in both | removed |
| **EXCEPT** / **MINUS** | Rows in first not in second | removed |

Requirements: same number of columns, compatible types, same order. Prefer **UNION ALL** when you know there are no duplicates (avoids the dedup sort — a real performance win).

**Join vs Set operation:** a join combines columns *horizontally* (adds columns from another table on a condition); a set operation stacks rows *vertically* (same columns, more rows).

## 3.3 GROUP BY, HAVING & aggregates
- **Aggregate functions:** `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`. They collapse many rows into one per group.
- **`GROUP BY`** defines the groups; every non-aggregated column in SELECT must appear in GROUP BY (standard SQL / most engines).
- **`WHERE` filters rows before grouping; `HAVING` filters groups after.**
```sql
SELECT dept_id, COUNT(*) AS headcount, AVG(salary) AS avg_sal
FROM employees
WHERE status = 'ACTIVE'          -- row filter (pre-group)
GROUP BY dept_id
HAVING COUNT(*) > 5              -- group filter (post-aggregation)
ORDER BY avg_sal DESC;
```

### COUNT gotchas (asked a lot)
- `COUNT(*)` — counts **all rows**, including NULLs.
- `COUNT(column)` — counts **non-NULL** values in that column.
- `COUNT(DISTINCT column)` — counts distinct non-NULL values.

### Aggregate vs scalar functions
- **Aggregate** — operate over a set of rows, return one value (`SUM`, `AVG`).
- **Scalar** — operate per row (`UPPER`, `ROUND`, `LENGTH`, `COALESCE`).

## 3.4 NULL handling (a top source of bugs & questions)
NULL means **unknown / missing**, not zero and not empty string.
- **Any comparison with NULL yields UNKNOWN**, not true/false. So `WHERE x = NULL` returns nothing — you must use `IS NULL` / `IS NOT NULL`.
- `NULL = NULL` is **not** true (it's UNKNOWN).
- Arithmetic with NULL → NULL (`5 + NULL = NULL`).
- Aggregates (`SUM`, `AVG`) **ignore NULLs**; `COUNT(col)` ignores NULLs; `COUNT(*)` doesn't.
- `NOT IN (subquery with a NULL)` returns **no rows** — a classic trap (use `NOT EXISTS` instead).

### NULL helper functions
```sql
COALESCE(a, b, c)   -- first non-NULL argument (portable)
NULLIF(a, b)        -- NULL if a = b, else a
IFNULL(a, b)        -- MySQL       | NVL(a,b) Oracle | ISNULL(a,b) SQL Server
```
```sql
SELECT name, COALESCE(commission, 0) AS commission FROM employees; -- treat NULL as 0
```

### Three-valued logic (TRUE / FALSE / UNKNOWN)
`WHERE` keeps only rows where the predicate is **TRUE** (UNKNOWN and FALSE are both excluded). This is why NULLs "disappear" from filters.

## 3.5 Interview Q&A

#### Q1. INNER JOIN vs LEFT JOIN, and the WHERE-vs-ON trap?
**Answer:** INNER JOIN returns only matching rows from both tables; LEFT JOIN returns all left rows plus matches, with NULLs where the right side has none. The trap: if you filter a right-table column in WHERE on a LEFT JOIN, the NULL rows fail the predicate and get dropped, silently turning it into an INNER JOIN. To keep unmatched rows, put that condition in the ON clause instead.

**Counter Q:** How do you find rows in A with no match in B?
**Answer:** An anti-join: `LEFT JOIN B ... WHERE B.key IS NULL`, or `NOT EXISTS (SELECT 1 FROM B WHERE ...)`. I prefer `NOT EXISTS` because it's NULL-safe, unlike `NOT IN`.

**Counter Q:** Why is `NOT IN` dangerous with NULLs?
**Answer:** If the subquery returns any NULL, `NOT IN` evaluates to UNKNOWN for every row and returns no rows at all. `NOT EXISTS` doesn't have this problem, so it's the safer choice.

#### Q2. WHERE vs HAVING?
**Answer:** WHERE filters individual rows before grouping and can't use aggregates. HAVING filters groups after aggregation and can use aggregate functions like COUNT/SUM. If a condition doesn't involve an aggregate, put it in WHERE — it's cheaper because it reduces rows before grouping.

**Counter Q:** `COUNT(*)` vs `COUNT(column)`?
**Answer:** `COUNT(*)` counts all rows including NULLs; `COUNT(column)` counts only non-NULL values in that column. `COUNT(DISTINCT column)` counts distinct non-NULLs.

#### Q3. What does NULL mean and how does it behave in comparisons?
**Answer:** NULL is unknown/missing. Comparisons with NULL return UNKNOWN, not true or false, so `= NULL` never matches — you use IS NULL. Aggregates ignore NULLs, arithmetic with NULL yields NULL, and WHERE keeps only rows where the predicate is TRUE, so NULL rows drop out of filters. I use COALESCE to substitute defaults.

**Counter Q:** UNION vs UNION ALL?
**Answer:** UNION removes duplicates (requires a sort/hash to dedup); UNION ALL keeps all rows and is faster. Use UNION ALL when duplicates are impossible or acceptable.

### Quick Revision
- INNER=matches; LEFT=all left+NULLs; anti-join = LEFT JOIN ... IS NULL or NOT EXISTS.
- Filter right table of LEFT JOIN in ON, not WHERE.
- UNION dedups (slow), UNION ALL keeps all (fast).
- WHERE pre-group, HAVING post-aggregate; COUNT(*) counts NULL rows, COUNT(col) doesn't.
- NULL = unknown; use IS NULL, COALESCE; avoid NOT IN with NULLs → NOT EXISTS.

---

# 4. Query Types + Rewrites + Optimization

This is the section interviewers love: *"solve it with a subquery... now do it with a join... now optimize it."* Learn to move fluidly between the three forms and explain the trade-offs.

## 4.1 Types of subquery (vocabulary you must have)
- **Scalar subquery** — returns a single value; usable anywhere a value is expected.
- **Row subquery** — returns a single row (multiple columns).
- **Table/multi-row subquery** — returns many rows; used with `IN`, `ANY`, `ALL`, `EXISTS`.
- **Correlated subquery** — references the outer query; **re-evaluated per outer row** (potentially slow).
- **Non-correlated (independent) subquery** — evaluated once, independent of the outer query.
- **Derived table** — a subquery in the `FROM` clause (aliased inline view).
- **CTE** — `WITH name AS (...)` a named subquery for readability/reuse/recursion.

### Correlated vs non-correlated (must-know)
```sql
-- NON-CORRELATED: inner runs once
SELECT name FROM employees
WHERE dept_id IN (SELECT dept_id FROM departments WHERE location = 'NY');

-- CORRELATED: inner references outer e; conceptually runs per outer row
SELECT e.name FROM employees e
WHERE e.salary > (SELECT AVG(salary) FROM employees x WHERE x.dept_id = e.dept_id);
```
**Interview line:** a correlated subquery depends on the outer row and is logically evaluated once per outer row, so it can be O(n·m); a good optimizer may rewrite it, but often a JOIN or window function is faster and clearer.

## 4.2 The classic rewrite drill: "employees earning above their department's average"

**Version A — Correlated subquery (what many write first):**
```sql
SELECT e.name, e.salary, e.dept_id
FROM employees e
WHERE e.salary > (SELECT AVG(x.salary) FROM employees x WHERE x.dept_id = e.dept_id);
```
- Simple to read, but the average is recomputed for each employee row.

**Version B — Join to a derived table (compute averages once):**
```sql
SELECT e.name, e.salary, e.dept_id
FROM employees e
JOIN (SELECT dept_id, AVG(salary) AS avg_sal
      FROM employees GROUP BY dept_id) d
  ON e.dept_id = d.dept_id
WHERE e.salary > d.avg_sal;
```
- The averages are computed **once** in the derived table, then joined — usually faster on large data.

**Version C — Window function (cleanest, single pass):**
```sql
SELECT name, salary, dept_id
FROM (
  SELECT name, salary, dept_id,
         AVG(salary) OVER (PARTITION BY dept_id) AS avg_sal
  FROM employees
) t
WHERE salary > avg_sal;
```
- One scan, no self-join; the window computes each department's average alongside every row. Often the best on modern engines.

**How to present this in an interview:** "I'd start with the correlated subquery because it maps directly to the requirement. But it re-evaluates the average per row, so I'd rewrite it to pre-aggregate once — either a JOIN to a grouped derived table, or a window function which avoids the extra join and does it in a single pass. I'd confirm with EXPLAIN which the optimizer prefers."

## 4.3 IN vs EXISTS vs JOIN (same result, different performance)

**Goal: customers who have placed at least one order.**
```sql
-- IN (non-correlated)
SELECT * FROM customers c
WHERE c.customer_id IN (SELECT o.customer_id FROM orders o);

-- EXISTS (correlated, short-circuits on first match)
SELECT * FROM customers c
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id);

-- JOIN (may produce duplicates -> needs DISTINCT)
SELECT DISTINCT c.* FROM customers c
JOIN orders o ON o.customer_id = c.customer_id;
```
**Guidance:**
- **EXISTS** stops at the first matching row — great when the inner table is large and you only care about existence.
- **IN** can be fine when the subquery list is small; beware NULLs with `NOT IN`.
- **JOIN** can duplicate rows (one per order) so you need DISTINCT, which adds a sort.
- Modern optimizers often treat IN/EXISTS/semi-join similarly, but **`NOT EXISTS` beats `NOT IN`** for "no match" because of NULL semantics.

**"Customers with NO orders" — the important NOT version:**
```sql
-- BEST: NOT EXISTS (NULL-safe)
SELECT * FROM customers c
WHERE NOT EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id);

-- Anti-join alternative
SELECT c.* FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id
WHERE o.order_id IS NULL;

-- AVOID: NOT IN breaks if any customer_id in orders is NULL
```

## 4.4 Types of query problems & the pattern to use
| Problem type | Best tool |
|---|---|
| Top-N / Nth highest | `LIMIT`/`OFFSET`, `DENSE_RANK()` window |
| Per-group Top-N (e.g., top earner per dept) | `ROW_NUMBER() OVER (PARTITION BY ...)` |
| Running total / cumulative | `SUM() OVER (ORDER BY ...)` |
| Compare to previous/next row | `LAG()` / `LEAD()` |
| Deduplicate keeping one | `ROW_NUMBER()` + filter, or GROUP BY |
| Existence / anti-join | `EXISTS` / `NOT EXISTS` |
| Pre-aggregate then filter | derived table / CTE + JOIN |
| Pivot (rows → columns) | conditional aggregation `SUM(CASE WHEN ...)` |
| Hierarchy traversal | recursive CTE |
| Gaps & islands (consecutive) | ROW_NUMBER difference trick |

## 4.5 Worked optimization example: department headcount + names

**Naive (correlated scalar subquery in SELECT — runs per row):**
```sql
SELECT d.dept_name,
       (SELECT COUNT(*) FROM employees e WHERE e.dept_id = d.dept_id) AS headcount
FROM departments d;
```
**Optimized (single GROUP BY + LEFT JOIN, one pass over employees):**
```sql
SELECT d.dept_name, COALESCE(cnt.headcount, 0) AS headcount
FROM departments d
LEFT JOIN (SELECT dept_id, COUNT(*) AS headcount
           FROM employees GROUP BY dept_id) cnt
  ON cnt.dept_id = d.dept_id;
```
**Why better:** the correlated version scans `employees` once per department; the grouped version scans employees once total and joins. LEFT JOIN + COALESCE keeps departments with zero employees (the correlated version already returns 0, but the join scales far better).

## 4.6 General rewrite/optimization principles (say these out loud)
1. **Filter early and narrowly** — push conditions into WHERE/ON; select only needed columns (avoid `SELECT *`).
2. **Pre-aggregate once** instead of recomputing per row (derived table/CTE/window).
3. **Prefer set-based over row-by-row** — one query beats a loop of queries (this is the app-side N+1 problem too).
4. **Make predicates sargable** — don't wrap indexed columns in functions (`WHERE YEAR(order_date)=2024` can't use the index; use a range `order_date >= '2024-01-01' AND < '2025-01-01'`).
5. **Avoid unnecessary DISTINCT / ORDER BY** (each forces a sort).
6. **UNION ALL over UNION** when dedup isn't needed.
7. **EXISTS/NOT EXISTS** over IN/NOT IN for large subqueries and NULL safety.
8. **Verify with EXPLAIN** — never guess; read the plan (Part 6).

## 4.7 Interview Q&A

#### Q1. Subquery vs join — which is faster?
**Answer:** It depends, and I'd verify with EXPLAIN. A correlated subquery is logically evaluated per outer row, so on large data a JOIN to a pre-aggregated derived table or a window function is usually faster because it computes the aggregate once. But modern optimizers often rewrite simple subqueries into joins internally, so for small data the difference can be negligible. My rule: write it clearly first, then optimize the hot ones based on the plan.

**Counter Q:** IN vs EXISTS?
**Answer:** EXISTS short-circuits on the first match, which is efficient when the inner set is large. IN materializes/checks the value list and is fine for small lists. For the negative case, NOT EXISTS is preferred over NOT IN because NOT IN returns no rows if the subquery contains any NULL.

**Counter Q:** When would you use a window function instead of GROUP BY?
**Answer:** When I need the aggregate **alongside each row** rather than collapsing rows — e.g., each employee with their department average, running totals, or rankings. GROUP BY reduces rows to one per group; a window keeps all rows and adds the computed value.

**Counter Q:** What does "sargable" mean?
**Answer:** Search-ARGument-able — a predicate that can use an index. Wrapping a column in a function or doing arithmetic on it (`WHERE UPPER(name)='X'`, `WHERE salary+100 > 500`) usually makes it non-sargable, forcing a scan. Rewriting to compare the bare column against a computed constant/range keeps it index-friendly.

### Quick Revision
- Subquery kinds: scalar/row/table, correlated vs not, derived table, CTE.
- Correlated ≈ per-row eval → often rewrite to JOIN/derived table/window.
- EXISTS short-circuits; NOT EXISTS beats NOT IN (NULLs).
- Pre-aggregate once; keep predicates sargable; UNION ALL when possible.
- Same result, three shapes — pick by EXPLAIN.

---

# 5. Indexing & Internals

## 5.1 What is an index and why?
An index is a separate, sorted data structure that lets the DB find rows **without scanning the whole table** — like a book's index. Without it, a lookup is a **full table scan** O(n); with a B-tree index it's O(log n).

**Trade-off:** indexes speed up reads (SELECT/WHERE/JOIN/ORDER BY) but **slow down writes** (every INSERT/UPDATE/DELETE must maintain them) and consume storage. So you index selectively, based on query patterns.

## 5.2 How indexes are stored — the B+ tree
Most relational indexes use a **B+ tree** (a balanced multi-way tree):
- **Balanced** — every leaf is at the same depth, so lookups take the same (logarithmic) number of steps.
- **High fan-out** — each node holds many keys, so the tree stays shallow (a few levels even for millions of rows) → few disk reads.
- **Leaf nodes are linked** — leaves form a sorted linked list, which makes **range scans** and **ORDER BY** on the indexed column efficient (walk the leaves).
- Internal nodes hold keys + pointers to guide the search; leaves hold the actual keys (and row pointers or data).

**Why B+ tree, not a binary tree or hash?** A binary tree is too deep (too many disk seeks). A **hash index** gives O(1) equality lookups but **can't do range/ORDER BY/prefix** queries and doesn't stay sorted — so B+ trees are the default general-purpose index.

## 5.3 Clustered vs non-clustered
| | Clustered | Non-clustered (secondary) |
|---|---|---|
| Stores | Table rows physically **sorted** by the key | Separate structure with key + pointer to the row |
| Count per table | **One** (defines physical order) | Many |
| Lookup | Data is *in* the index leaves | Leaf points to the row (extra step) |
| Example | InnoDB primary key | Any additional index |

- **MySQL/InnoDB:** the PRIMARY KEY **is** the clustered index; secondary indexes store the **PK value** as the row pointer (so a secondary lookup does a second lookup into the clustered index unless it's covering).
- **PostgreSQL:** all indexes are effectively secondary (heap table); `CLUSTER` only reorders once, not maintained.
- **SQL Server:** one clustered index per table (often the PK).

## 5.4 Composite (multi-column) index & the leftmost-prefix rule
```sql
CREATE INDEX idx_emp ON employees (dept_id, salary);
```
This index is usable for:
- `WHERE dept_id = ?`  ✅ (leftmost column)
- `WHERE dept_id = ? AND salary > ?`  ✅
- `WHERE salary > ?` **alone** ❌ (skips the leftmost column)

**Leftmost-prefix rule:** a composite index `(a, b, c)` can serve queries filtering on `a`, `a,b`, or `a,b,c` — a left-anchored prefix — but not `b` or `c` alone. **Column order matters:** put the most selective / most-frequently-filtered column first (and equality columns before range columns).

## 5.5 Covering index
An index that contains **all columns a query needs**, so the query is answered **from the index alone** without touching the table ("index-only scan").
```sql
-- Query: SELECT salary FROM employees WHERE dept_id = 10;
CREATE INDEX idx_cover ON employees (dept_id, salary); -- covers it: no table lookup needed
```
This avoids the extra "look up the row" step and is a big optimization for hot queries.

## 5.6 When an index is NOT used (very common interview question)
- **Function/expression on the column:** `WHERE YEAR(order_date) = 2024` or `WHERE UPPER(name) = 'X'` — the raw column isn't compared, so the index can't be used. Rewrite as a range or add a functional index.
- **Leading wildcard:** `LIKE '%abc'` can't use a normal B-tree (no prefix). `LIKE 'abc%'` can.
- **Low selectivity:** a column with few distinct values (e.g., boolean `is_active`) — the optimizer prefers a scan because the index would match most rows.
- **Implicit type conversion:** comparing an indexed string column to a number (or mismatched collation) can disable the index.
- **`OR` across different columns** sometimes prevents index use (may need index merge or a UNION rewrite).
- **Small tables:** a full scan is cheaper than index overhead, so the optimizer skips the index.
- **Not the leftmost prefix** of a composite index.

## 5.7 Selectivity & cardinality
- **Cardinality** — number of distinct values in a column. **Selectivity** — distinct values / total rows (higher = more selective).
- Indexes help most on **high-selectivity** columns (email, id) and least on low-selectivity ones (gender, boolean). The optimizer uses **statistics** (histograms) to estimate this; stale stats → bad plans (run `ANALYZE`).

## 5.8 Other index types (know they exist)
- **Unique index** — enforces uniqueness (backs UNIQUE/PK constraints).
- **Hash index** — O(1) equality only (no ranges); Postgres has them, MySQL memory engine.
- **Full-text index** — text search (`MATCH ... AGAINST`, `tsvector`).
- **Bitmap index** — great for low-cardinality columns in data warehouses (Oracle).
- **Partial/filtered index** — indexes a subset of rows (`WHERE status='ACTIVE'`) — smaller, faster.
- **Functional/expression index** — indexes `LOWER(email)` so function-based lookups become sargable.

## 5.9 Interview Q&A

#### Q1. How does a B-tree index make lookups fast?
**Answer:** It's a balanced tree with high fan-out, so even for millions of rows it's only a few levels deep — a lookup touches a handful of nodes, giving O(log n) instead of scanning every row. Leaves are kept sorted and linked, so range scans and ORDER BY on the indexed column are efficient too. That balance of equality plus range support is why B+ trees are the default over hash indexes.

**Counter Q:** Clustered vs non-clustered index?
**Answer:** A clustered index defines the physical order of the table rows, so the data lives in the index leaves — one per table. A non-clustered index is a separate structure holding the key and a pointer to the row, so it needs an extra lookup to fetch other columns. In InnoDB the primary key is the clustered index and secondary indexes store the PK as their pointer.

**Counter Q:** Why doesn't `WHERE YEAR(order_date) = 2024` use the index on order_date?
**Answer:** Because the index stores `order_date`, not `YEAR(order_date)`. Wrapping the column in a function makes the predicate non-sargable, so the engine must compute the function for every row — a full scan. I'd rewrite it as a range: `order_date >= '2024-01-01' AND order_date < '2025-01-01'`, which uses the index.

**Counter Q:** You have a composite index (a, b). Does `WHERE b = 5` use it?
**Answer:** No — that skips the leftmost column `a`, so the index can't be seeked (at best a full index scan). Composite indexes follow the leftmost-prefix rule, so I'd order columns by how they're queried, equality before range.

**Counter Q:** When would adding an index hurt?
**Answer:** On write-heavy tables (every write maintains the index), on low-selectivity columns (the optimizer won't use it and it wastes space), or when you already have a covering/overlapping index. Indexes aren't free — I add them for real query patterns and drop unused ones.

**Counter Q:** What is a covering index?
**Answer:** An index that includes every column a query reads, so the query is satisfied entirely from the index without touching the table — an index-only scan. It removes the row-fetch step and is a strong optimization for frequent queries.

### Quick Revision
- Index = sorted structure → O(log n) instead of full scan; slows writes.
- B+ tree: balanced, high fan-out, linked sorted leaves → equality + range.
- Clustered = data ordered by key (one); non-clustered = pointer to row (many).
- Composite index = leftmost-prefix rule; equality cols first.
- Covering index = answered from index alone (index-only scan).
- Not used when: function on column, leading %, low selectivity, type mismatch, non-leftmost, tiny table.

---

# 6. Query Optimization, EXPLAIN & Performance

## 6.1 How the optimizer works (theory)
The **query optimizer** is a cost-based component that considers multiple execution plans (join orders, index vs scan, join algorithms) and picks the cheapest based on **statistics** (row counts, value distribution/histograms, index selectivity). You don't tell it *how* — you write *what* you want, it decides *how*. Stale statistics lead to bad plans, so keep them fresh (`ANALYZE` / auto-analyze).

**Join algorithms it chooses among:**
- **Nested loop join** — for each outer row, probe the inner (great when inner side is indexed & small).
- **Hash join** — build a hash table on one side, probe with the other (great for large unindexed equijoins).
- **Merge join** — merge two sorted inputs (great when both are already sorted / indexed).

## 6.2 Reading EXPLAIN (what to look for)
`EXPLAIN` shows the plan; `EXPLAIN ANALYZE` (Postgres) / `EXPLAIN ANALYZE`/`EXPLAIN FORMAT=JSON` (MySQL 8) actually runs it and shows real timings/rows.

**Red flags in a plan:**
- **Seq Scan / Full Table Scan** on a large table where you expected an index.
- **Rows estimate wildly off** from actual → stale statistics.
- **Nested loop over a large unindexed inner table** → should be a hash join or needs an index.
- **Sort / Hash spilling to disk** → work_mem too small, or unnecessary ORDER BY/DISTINCT.
- **Filesort / Using temporary** (MySQL) → sorting/grouping without a usable index.

**Access types (MySQL `type`, best → worst):** `const` / `eq_ref` / `ref` (index lookups) → `range` (index range) → `index` (full index scan) → `ALL` (full table scan).

```sql
EXPLAIN ANALYZE
SELECT e.name, d.dept_name
FROM employees e JOIN departments d ON e.dept_id = d.dept_id
WHERE e.salary > 100000;
-- Look for: index used on employees.salary/dept_id? join type? estimated vs actual rows?
```

## 6.3 Performance optimization techniques (the checklist to recite)
1. **Add the right indexes** — on columns in WHERE, JOIN, ORDER BY, GROUP BY; composite in the right order; covering for hot queries.
2. **Keep predicates sargable** — no functions/arithmetic on indexed columns; use ranges instead of `YEAR()`, avoid leading `%` in LIKE.
3. **Select only needed columns** — avoid `SELECT *` (enables covering indexes, less IO).
4. **Filter early** — reduce rows before joins/aggregation; push conditions down.
5. **Pre-aggregate once** — derived table/CTE/window instead of correlated per-row work.
6. **Avoid unnecessary sorts** — remove needless DISTINCT/ORDER BY; UNION ALL over UNION.
7. **Batch writes** — multi-row INSERT / batch updates instead of row-by-row round trips.
8. **Paginate with keyset** for deep pages (see below).
9. **Fix N+1** at the app layer — one set-based query instead of a query per row.
10. **Update statistics** and consider partitioning very large tables.
11. **Right-size the connection pool & transactions** — keep transactions short so locks/connections release quickly.
12. **Denormalize / materialized views** for expensive read-heavy aggregates.

## 6.4 Pagination: OFFSET vs keyset (cursor)
```sql
-- OFFSET: simple but slow on deep pages (DB scans+discards all skipped rows)
SELECT * FROM orders ORDER BY order_id LIMIT 20 OFFSET 100000;  -- reads 100020 rows

-- KEYSET / cursor: fast and stable; uses the index on the sort key
SELECT * FROM orders
WHERE order_id > :last_seen_id      -- remember the last id from previous page
ORDER BY order_id
LIMIT 20;                            -- reads only 20 rows
```
**Why keyset wins:** OFFSET still reads and throws away all skipped rows (O(offset)), and can skip/duplicate rows if data changes between pages. Keyset seeks directly via the index (O(log n) + page size) and is stable. Downside: no random "jump to page 500", only next/previous.

## 6.5 The N+1 problem (app + SQL view)
Loading a list, then firing one extra query per item = 1 + N queries. In SQL terms it's row-by-row instead of set-based.
```text
SELECT * FROM orders;                          -- 1 query returns N orders
-- then per order:
SELECT * FROM customers WHERE id = ?;          -- N queries  -> N+1 total
```
**Fix:** a single JOIN (`SELECT ... FROM orders o JOIN customers c ON ...`) or an `IN (...)` batch. In ORMs: JOIN FETCH / EntityGraph / batch fetching.

## 6.6 Other performance concepts
- **Materialized view** — a stored, precomputed query result you refresh periodically; fast reads for expensive aggregates (vs a plain **view**, which is just a saved query re-run each time).
- **Partitioning** — split a huge table by range/list/hash (e.g., by month) so queries touch only relevant partitions (**partition pruning**) and maintenance is easier.
- **Sharding** — horizontal split across servers (scale-out); adds cross-shard complexity.
- **Read replicas** — offload reads to copies of the DB (eventual consistency).

## 6.7 Interview Q&A

#### Q1. Walk me through how you'd optimize a slow query.
**Answer:** First reproduce it and run EXPLAIN ANALYZE to see the actual plan — I look for full scans on big tables, bad row estimates (stale stats), expensive sorts, and nested loops over unindexed inner tables. Then I target the cause: add or fix indexes (right columns, right order, maybe covering), make predicates sargable, select only needed columns, filter earlier, and replace correlated per-row work with a single set-based query or window function. I re-check the plan and measure the improvement rather than guessing.

**Counter Q:** How does the optimizer decide between an index and a full scan?
**Answer:** It's cost-based — it estimates how many rows the predicate matches using column statistics. If the predicate is highly selective (few rows), an index seek is cheaper; if it matches a large fraction of the table, a sequential scan is actually cheaper because random index lookups cost more per row. That's why low-selectivity columns and small tables often skip the index.

**Counter Q:** Offset vs keyset pagination?
**Answer:** OFFSET reads and discards all skipped rows, so deep pages get linearly slower and can drift if data changes. Keyset pagination filters `WHERE key > last_seen` on an indexed sort column, seeking directly to the next page — fast and stable. The trade-off is you can only page sequentially, not jump to an arbitrary page.

**Counter Q:** Nested loop vs hash join?
**Answer:** Nested loop probes the inner table for each outer row — efficient when the inner side is small or indexed. Hash join builds a hash table on one input and probes with the other — efficient for large unindexed equijoins. The optimizer picks based on sizes and available indexes.

**Counter Q:** What is a covering index vs a materialized view?
**Answer:** A covering index answers a query from the index alone (no table fetch). A materialized view stores the precomputed result of a whole query and is refreshed periodically — better for expensive multi-table aggregates, at the cost of staleness between refreshes.

### Quick Revision
- Cost-based optimizer uses statistics; keep them fresh (ANALYZE).
- EXPLAIN red flags: full scan on big table, bad estimates, disk sort, nested loop on unindexed inner.
- Optimize: index right, sargable predicates, no SELECT *, filter early, pre-aggregate, batch, keyset paginate, fix N+1.
- Join algos: nested loop (indexed/small inner), hash (big equijoin), merge (sorted inputs).
- Materialized view = stored result; partitioning/sharding/replicas for scale.

---

# 7. Transactions, ACID, Isolation, Locking & Deadlocks

## 7.1 What is a transaction?
A **transaction** is a single logical unit of work — a group of one or more statements that must **all succeed or all fail** together. Classic example: a bank transfer (debit one account, credit another) must be atomic.
```sql
BEGIN;                                  -- or START TRANSACTION
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;                                 -- persist both; or ROLLBACK to undo both
```
- **COMMIT** — make changes permanent.
- **ROLLBACK** — undo all changes since the transaction began.
- **SAVEPOINT** — a marker to roll back *part* of a transaction.
- **autocommit** — when on (default in many clients), each statement is its own transaction.

## 7.2 ACID (know each with a one-line "what if it's missing")
- **Atomicity** — all-or-nothing. *Without it:* the debit could happen without the credit → money lost.
- **Consistency** — a transaction moves the DB from one valid state to another (all constraints hold). *Without it:* invalid data (negative balances, orphan FKs).
- **Isolation** — concurrent transactions don't interfere; results are as if they ran serially (to the degree the isolation level allows). *Without it:* dirty reads, lost updates.
- **Durability** — once committed, data survives crashes (written to disk/WAL). *Without it:* committed data lost on power failure.

**How they're implemented (bonus depth):** Atomicity/Durability via a **write-ahead log (WAL)/redo-undo log** — changes are logged before being applied, so the DB can replay (redo) committed work and undo incomplete work after a crash. Isolation via **locking or MVCC**. Consistency via constraints + the other three.

## 7.3 Read phenomena (the problems isolation prevents)
- **Dirty read** — read another transaction's **uncommitted** change that might roll back.
- **Non-repeatable read** — read the **same row** twice and get different values because another transaction **updated & committed** in between.
- **Phantom read** — run the **same query** twice and get different **sets of rows** because another transaction **inserted/deleted** matching rows.
- **Lost update** — two transactions read the same value, both update, and one overwrite is lost.

## 7.4 Isolation levels
| Level | Dirty read | Non-repeatable | Phantom |
|---|---|---|---|
| **READ UNCOMMITTED** | ✅ possible | ✅ | ✅ |
| **READ COMMITTED** | ❌ | ✅ | ✅ |
| **REPEATABLE READ** | ❌ | ❌ | ✅ (prevented in MySQL InnoDB via next-key locks) |
| **SERIALIZABLE** | ❌ | ❌ | ❌ |

- Higher isolation = fewer anomalies but **less concurrency** (more locking/aborts).
- **Defaults:** PostgreSQL & Oracle = **READ COMMITTED**; MySQL InnoDB = **REPEATABLE READ**.
- **SERIALIZABLE** makes transactions behave as if run one at a time (via strict locking or serialization checks that may abort/retry).

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

## 7.5 Locking
- **Shared (S) lock** — for reads; multiple transactions can hold it together.
- **Exclusive (X) lock** — for writes; only one holder, blocks others (readers and writers).
- **Lock granularity** — row-level (fine, high concurrency), page-level, table-level (coarse, low concurrency). Row locks are typical for OLTP.
- **Pessimistic locking** — lock the row up front so others wait (`SELECT ... FOR UPDATE`). Good for high-contention critical updates.
- **Optimistic locking** — don't lock; detect conflicts at write time via a **version column** (or timestamp); if the version changed, the update fails and you retry. Good for low-contention, high-throughput.

```sql
-- Pessimistic: lock the row for the duration of the transaction
BEGIN;
SELECT balance FROM accounts WHERE id = 1 FOR UPDATE;   -- others block on this row
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;

-- Optimistic: succeed only if nobody changed it since we read (version = 7)
UPDATE items SET stock = stock - 1, version = version + 1
WHERE id = 42 AND version = 7;   -- 0 rows affected => conflict => retry
```

## 7.6 MVCC (Multi-Version Concurrency Control) — modern isolation
Postgres/MySQL-InnoDB/Oracle use **MVCC**: instead of readers blocking writers, each transaction sees a **consistent snapshot** of the data as of its start (or statement). Writers create **new versions** of rows; readers keep reading the old version until they'd see the committed one.
- **Key benefit:** **readers don't block writers and writers don't block readers** — much higher concurrency than pure locking.
- Old row versions are cleaned up later (Postgres `VACUUM`, InnoDB purge).

## 7.7 Deadlocks
A **deadlock** is a circular wait: T1 holds lock A and wants B; T2 holds B and wants A — neither can proceed.
```text
T1: lock row 1 ... then wants row 2
T2: lock row 2 ... then wants row 1   -> deadlock
```
- The DB has a **deadlock detector**; it aborts one transaction (the "victim"), which rolls back so the other proceeds. Your app must **catch the error and retry**.
- **Prevention:** always acquire locks in a **consistent order** (e.g., always lock the lower id first), keep transactions **short**, use appropriate isolation, and avoid user think-time inside transactions.

## 7.8 Interview Q&A

#### Q1. Explain ACID.
**Answer:** Atomicity means all statements in a transaction succeed or none do. Consistency means it leaves the database valid — all constraints satisfied. Isolation means concurrent transactions don't step on each other, ideally appearing to run serially. Durability means once committed, the data survives crashes. Databases implement atomicity and durability with a write-ahead log and isolation with locking or MVCC.

**Counter Q:** Explain the three read phenomena and which isolation level prevents each.
**Answer:** A dirty read is reading uncommitted data — prevented from READ COMMITTED up. A non-repeatable read is the same row returning different values on re-read due to a committed update — prevented from REPEATABLE READ up. A phantom read is the same query returning different rows due to inserts/deletes — prevented at SERIALIZABLE (and by InnoDB's next-key locks at REPEATABLE READ). Higher levels prevent more but reduce concurrency.

**Counter Q:** Optimistic vs pessimistic locking — when each?
**Answer:** Pessimistic locking takes an exclusive lock up front (`SELECT ... FOR UPDATE`) so others wait — good when contention is high and conflicts are likely, at the cost of blocking. Optimistic locking uses a version column and only detects a conflict at update time, failing and retrying — good when contention is low and you want maximum throughput without holding locks.

**Counter Q:** What is MVCC and why is it better than locking for reads?
**Answer:** MVCC keeps multiple versions of rows so each transaction reads a consistent snapshot without acquiring read locks. Readers don't block writers and writers don't block readers, so concurrency is much higher than with pure locking. The cost is storing and later cleaning up old versions (VACUUM/purge).

**Counter Q:** What causes a deadlock and how do you handle it?
**Answer:** A circular wait where each transaction holds a lock the other needs. The database detects it and aborts one as a victim; the app should catch the deadlock error and retry. To prevent them I acquire locks in a consistent order, keep transactions short, and avoid interactive waits inside transactions.

**Counter Q:** What's a lost update and how do you prevent it?
**Answer:** Two transactions read the same value, both modify it, and one write silently overwrites the other. I prevent it with optimistic locking (version check) or pessimistic locking (`SELECT ... FOR UPDATE`), or by doing the update atomically in one statement (`SET stock = stock - 1`).

### Quick Revision
- Transaction = all-or-nothing unit; COMMIT/ROLLBACK/SAVEPOINT.
- ACID: Atomicity, Consistency, Isolation, Durability (WAL + locking/MVCC).
- Phenomena: dirty / non-repeatable / phantom / lost update.
- Levels: READ UNCOMMITTED < READ COMMITTED < REPEATABLE READ < SERIALIZABLE.
- Pessimistic (FOR UPDATE) vs optimistic (version check); MVCC = snapshot reads.
- Deadlock = circular wait → DB aborts a victim → retry; prevent via consistent lock order + short txns.

---

# 8. Advanced SQL

## 8.1 Window (analytic) functions
Compute a value **across a set of rows related to the current row** — without collapsing rows like GROUP BY does.
```sql
function() OVER (PARTITION BY col ORDER BY col ROWS BETWEEN ... )
```
- **PARTITION BY** — split rows into groups (like GROUP BY but keeps all rows).
- **ORDER BY** — order within the partition (needed for ranking/running totals).
- **Frame** (`ROWS`/`RANGE BETWEEN`) — the window of rows used (e.g., running total).

### Ranking functions (know the differences)
```sql
SELECT name, dept_id, salary,
  ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rn,   -- 1,2,3,4 (no ties)
  RANK()       OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rnk,  -- 1,2,2,4 (gaps)
  DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS drnk  -- 1,2,2,3 (no gaps)
FROM employees;
```
- **ROW_NUMBER** — unique sequential number, ties broken arbitrarily.
- **RANK** — same rank for ties, then **skips** numbers (1,2,2,4).
- **DENSE_RANK** — same rank for ties, **no skips** (1,2,2,3).
- **NTILE(n)** — split rows into n buckets (quartiles etc.).

### LAG / LEAD (compare to previous/next row)
```sql
-- Month-over-month change
SELECT month, revenue,
       LAG(revenue) OVER (ORDER BY month) AS prev_revenue,
       revenue - LAG(revenue) OVER (ORDER BY month) AS change
FROM monthly_sales;
```

### Running / cumulative total
```sql
SELECT order_date, amount,
       SUM(amount) OVER (ORDER BY order_date
                         ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total
FROM orders;
```

**Window vs GROUP BY (interview line):** GROUP BY collapses each group into one row; a window function keeps every row and attaches the computed aggregate/rank alongside it. Use windows when you need per-row detail *and* a group-level calculation together.

## 8.2 CTEs (Common Table Expressions)
`WITH` defines a named, reusable subquery — improves readability and enables recursion.
```sql
WITH dept_avg AS (
    SELECT dept_id, AVG(salary) AS avg_sal FROM employees GROUP BY dept_id
)
SELECT e.name, e.salary, d.avg_sal
FROM employees e JOIN dept_avg d ON e.dept_id = d.dept_id
WHERE e.salary > d.avg_sal;
```
**CTE vs subquery/derived table:** functionally similar; a CTE is named, can be referenced multiple times, reads top-to-bottom (more readable), and supports recursion. (In some engines a CTE is an optimization fence — check your DB.)

### Recursive CTE (hierarchies — org chart, categories)
```sql
WITH RECURSIVE org AS (
    SELECT emp_id, name, manager_id, 1 AS level      -- anchor: top managers
    FROM employees WHERE manager_id IS NULL
    UNION ALL
    SELECT e.emp_id, e.name, e.manager_id, o.level + 1  -- recursive step
    FROM employees e JOIN org o ON e.manager_id = o.emp_id
)
SELECT * FROM org ORDER BY level;
```
Runs the anchor, then repeatedly joins the recursive part until no new rows — perfect for tree/graph traversal.

## 8.3 CASE (conditional logic)
```sql
SELECT name,
  CASE WHEN salary >= 100000 THEN 'High'
       WHEN salary >= 50000  THEN 'Mid'
       ELSE 'Low' END AS band
FROM employees;
```

## 8.4 Pivot (rows → columns) via conditional aggregation
```sql
-- Count orders per status, one column each (portable pivot)
SELECT
  customer_id,
  SUM(CASE WHEN status = 'PAID'     THEN 1 ELSE 0 END) AS paid,
  SUM(CASE WHEN status = 'PENDING'  THEN 1 ELSE 0 END) AS pending,
  SUM(CASE WHEN status = 'CANCELLED'THEN 1 ELSE 0 END) AS cancelled
FROM orders
GROUP BY customer_id;
```
This is the portable way to pivot; some engines have a `PIVOT` operator (SQL Server/Oracle).

## 8.5 Views
A **view** is a stored, named query — a virtual table.
```sql
CREATE VIEW active_customers AS
SELECT customer_id, name, email FROM customers WHERE created_at > '2024-01-01';
```
- **Why:** simplify complex queries, provide a stable interface, restrict columns/rows for security.
- **View vs materialized view:** a view re-runs its query every time (always fresh, no storage); a **materialized view** stores the result and must be refreshed (fast reads, can be stale).
- **Updatable views:** simple single-table views can be updated; complex ones (joins/aggregates) usually can't.

## 8.6 Stored procedures, functions & triggers (theory)
- **Stored procedure** — precompiled routine stored in the DB; can take params, run multiple statements, control flow. Reduces round trips and centralizes logic. (No required return; may return result sets/out params.)
- **Function (UDF)** — returns a value; usable inside queries (`SELECT my_fn(x)`); should be side-effect-free.
- **Trigger** — code that fires automatically on `INSERT`/`UPDATE`/`DELETE` (`BEFORE`/`AFTER`). Used for auditing, enforcing rules, maintaining denormalized data.
  - **Caution:** triggers hide logic and can hurt performance/debuggability; many teams prefer application logic.
- **Procedure vs function:** a function returns a value and is called within SQL expressions and generally can't modify data (engine-dependent); a procedure is invoked with `CALL`, can perform actions/transactions, and needn't return a value.

## 8.7 Interview Q&A

#### Q1. ROW_NUMBER vs RANK vs DENSE_RANK?
**Answer:** All assign a ranking within an ordered partition. ROW_NUMBER always gives distinct sequential numbers even for ties. RANK gives ties the same number but then skips (1,2,2,4). DENSE_RANK gives ties the same number with no gaps (1,2,2,3). For "Nth highest distinct salary" I use DENSE_RANK; for "one row per group" I use ROW_NUMBER.

**Counter Q:** GROUP BY vs window function?
**Answer:** GROUP BY collapses each group to a single row, so you lose row-level detail. A window function computes the aggregate or rank but keeps every row, attaching the value. Use a window when you need both the individual rows and a group-level calculation, like each employee alongside their department average.

**Counter Q:** What is a recursive CTE for?
**Answer:** Traversing hierarchical or graph data — org charts, category trees, bill-of-materials. It has an anchor query for the starting rows and a recursive part that repeatedly joins back until no new rows are produced.

**Counter Q:** View vs materialized view?
**Answer:** A view is just a stored query re-executed each time, always current with no storage cost. A materialized view stores the computed result for fast reads and must be refreshed, so it can be stale — a good fit for expensive aggregates that don't need to be real-time.

**Counter Q:** When would you avoid triggers?
**Answer:** When the logic is better kept visible in the application, when they'd add hidden performance cost to every write, or when they make behavior hard to trace and test. I use them mainly for reliable auditing or maintaining derived columns close to the data.

### Quick Revision
- Window fn = per-row + group calc; PARTITION BY/ORDER BY/frame.
- ROW_NUMBER (unique), RANK (gaps), DENSE_RANK (no gaps); LAG/LEAD for prev/next; SUM() OVER for running totals.
- CTE = named subquery (readable, reusable, recursive for hierarchies).
- Pivot via SUM(CASE WHEN...); view = stored query, materialized = stored result.
- Procedure (action, CALL) vs function (returns value, in queries); triggers auto-fire on DML.

---

# 9. Interview Query Problem Bank

Each problem shows the approach and multiple solutions where relevant. Uses the sample schema.

### 9.1 Nth highest salary (the #1 SQL interview question)
```sql
-- 2nd highest (subquery)
SELECT MAX(salary) FROM employees WHERE salary < (SELECT MAX(salary) FROM employees);

-- Nth highest (DENSE_RANK — handles ties as "distinct" values)
SELECT DISTINCT salary FROM (
  SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk FROM employees
) t WHERE rnk = 3;                     -- N = 3

-- Nth highest (LIMIT/OFFSET — MySQL/Postgres; NOT distinct-aware)
SELECT DISTINCT salary FROM employees ORDER BY salary DESC LIMIT 1 OFFSET 2;  -- N-1 offset
```
**Follow-up "what if there are ties / duplicate salaries?"** → Use DENSE_RANK for distinct salary ranks; ROW_NUMBER if you want a specific single row.

### 9.2 Top N per group (top earner per department)
```sql
SELECT * FROM (
  SELECT e.*, ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rn
  FROM employees e
) t WHERE rn = 1;                      -- top 1 per dept; rn <= 3 for top 3
```

### 9.3 Find duplicates
```sql
-- Rows with duplicate email
SELECT email, COUNT(*) AS cnt
FROM customers GROUP BY email HAVING COUNT(*) > 1;

-- List the actual duplicate rows (keep first, flag rest)
SELECT * FROM (
  SELECT c.*, ROW_NUMBER() OVER (PARTITION BY email ORDER BY customer_id) AS rn
  FROM customers c
) t WHERE rn > 1;
```

### 9.4 Delete duplicates keeping the lowest id
```sql
-- Portable (CTE + ROW_NUMBER)
WITH d AS (
  SELECT customer_id,
         ROW_NUMBER() OVER (PARTITION BY email ORDER BY customer_id) AS rn
  FROM customers
)
DELETE FROM customers WHERE customer_id IN (SELECT customer_id FROM d WHERE rn > 1);

-- MySQL self-join style
DELETE c1 FROM customers c1
JOIN customers c2 ON c1.email = c2.email AND c1.customer_id > c2.customer_id;
```

### 9.5 Departments with no employees (anti-join)
```sql
SELECT d.dept_name FROM departments d
WHERE NOT EXISTS (SELECT 1 FROM employees e WHERE e.dept_id = d.dept_id);
```

### 9.6 Second highest per department + who
```sql
SELECT name, dept_id, salary FROM (
  SELECT name, dept_id, salary,
         DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS drnk
  FROM employees
) t WHERE drnk = 2;
```

### 9.7 Running total & cumulative % (windows)
```sql
SELECT order_date, amount,
  SUM(amount) OVER (ORDER BY order_date) AS running_total,
  ROUND(100.0 * SUM(amount) OVER (ORDER BY order_date)
        / SUM(amount) OVER (), 2) AS cumulative_pct
FROM orders;
```

### 9.8 Month-over-month growth (LAG)
```sql
SELECT month, revenue,
  revenue - LAG(revenue) OVER (ORDER BY month) AS mom_change,
  ROUND(100.0 * (revenue - LAG(revenue) OVER (ORDER BY month))
        / NULLIF(LAG(revenue) OVER (ORDER BY month), 0), 2) AS mom_pct
FROM monthly_sales;
```
(`NULLIF(..., 0)` avoids divide-by-zero.)

### 9.9 Consecutive rows / "gaps and islands" (e.g., consecutive login days)
```sql
-- Users who logged in 3+ consecutive days: the ROW_NUMBER date-diff trick
WITH g AS (
  SELECT user_id, login_date,
         login_date - (ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY login_date))::int AS grp
  FROM logins
)
SELECT user_id, MIN(login_date) AS streak_start, COUNT(*) AS streak_len
FROM g GROUP BY user_id, grp HAVING COUNT(*) >= 3;
```
**Idea:** for consecutive dates, `date - row_number` is constant within a run, so grouping on it isolates each streak ("island"). This is *the* pattern for consecutive/streak questions.

### 9.10 Median salary per department
```sql
-- Portable with PERCENTILE_CONT (Postgres/Oracle)
SELECT dept_id,
  PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) AS median_salary
FROM employees GROUP BY dept_id;
```

### 9.11 Pivot: order counts by status per customer
```sql
SELECT customer_id,
  SUM(CASE WHEN status='PAID' THEN 1 ELSE 0 END)    AS paid,
  SUM(CASE WHEN status='PENDING' THEN 1 ELSE 0 END) AS pending
FROM orders GROUP BY customer_id;
```

### 9.12 Employees earning more than their manager (self join)
```sql
SELECT e.name AS employee, e.salary, m.name AS manager, m.salary AS mgr_salary
FROM employees e JOIN employees m ON e.manager_id = m.emp_id
WHERE e.salary > m.salary;
```

### 9.13 Customers who bought ALL products (relational division)
```sql
SELECT c.customer_id
FROM customers c
WHERE NOT EXISTS (
  SELECT 1 FROM products p
  WHERE NOT EXISTS (
    SELECT 1 FROM orders o
    WHERE o.customer_id = c.customer_id AND o.product_id = p.product_id
  )
);  -- "no product that the customer did NOT buy" = bought all
```

### 9.14 Nth highest — the "now optimize it" progression
Interviewers escalate: *"solve it, now without LIMIT, now handle ties, now what index helps?"*
- Base: `ORDER BY salary DESC LIMIT 1 OFFSET n-1`.
- No LIMIT / portable: `DENSE_RANK()` window (handles ties, works across engines).
- Ties: DENSE_RANK gives distinct-value ranking.
- **Index:** an index on `salary` lets ranking/ordering avoid a full sort — mention it proactively.

### 9.15 Grouped aggregation with filter, then rank
```sql
-- Top 3 departments by total salary, only active employees
SELECT dept_id, total FROM (
  SELECT dept_id, SUM(salary) AS total,
         RANK() OVER (ORDER BY SUM(salary) DESC) AS rnk
  FROM employees WHERE status='ACTIVE' GROUP BY dept_id
) t WHERE rnk <= 3;
```

---

# 10. Theory Q&A with Counter-Questions

Rapid, high-frequency theory questions with interview-ready answers.

#### Q. DBMS vs RDBMS?
DBMS manages data generally; RDBMS uses the relational model (tables + keys), enforces relationships/ACID, and uses SQL. Every RDBMS is a DBMS.
- **Why relational over files?** Reduced redundancy, integrity constraints, concurrency control, transactions, and a declarative query language.

#### Q. Primary key vs unique key vs candidate key?
Candidate = minimal unique identifier; the primary key is a chosen candidate (not null, one per table); unique key enforces uniqueness elsewhere (allows a null, many per table).
- **Can PK be composite?** Yes, multiple columns together.

#### Q. Explain normalization / 3NF in one line.
Organize data so each fact is stored once: 1NF atomic, 2NF no partial dependency, 3NF no transitive dependency, BCNF every determinant is a super key.
- **Why denormalize then?** Read performance — fewer joins at the cost of controlled redundancy.

#### Q. Clustered vs non-clustered index?
Clustered orders the table physically by the key (one per table, data in leaves); non-clustered is a separate structure pointing to rows (many per table).
- **What does InnoDB use as clustered?** The primary key.

#### Q. Why might an index not be used?
Function on the column, leading wildcard LIKE, low selectivity, type mismatch, non-leftmost composite column, or a small table where a scan is cheaper.
- **Fix for `WHERE YEAR(col)=2024`?** Rewrite as a date range so it's sargable.

#### Q. WHERE vs HAVING?
WHERE filters rows before grouping (no aggregates); HAVING filters groups after aggregation.
- **Why can't WHERE use aggregates?** Grouping hasn't happened yet in the execution order.

#### Q. DELETE vs TRUNCATE vs DROP?
DELETE removes filtered rows (logged, rollbackable, fires triggers); TRUNCATE removes all rows fast (DDL, resets identity, no triggers); DROP removes the table structure.

#### Q. ACID?
Atomicity (all-or-nothing), Consistency (valid states), Isolation (no interference), Durability (survives crash). WAL + locking/MVCC implement them.

#### Q. Isolation levels and the read phenomena?
READ UNCOMMITTED→READ COMMITTED→REPEATABLE READ→SERIALIZABLE prevent progressively dirty, non-repeatable, and phantom reads. Higher = safer but less concurrent.

#### Q. Optimistic vs pessimistic locking?
Pessimistic locks up front (`FOR UPDATE`), optimistic detects conflicts via a version column at write time and retries. Pessimistic for high contention, optimistic for high throughput.

#### Q. What is a deadlock and how to handle it?
Circular lock wait; the DB aborts one transaction; app retries. Prevent with consistent lock ordering and short transactions.

#### Q. Subquery vs correlated subquery?
A subquery runs independently once; a correlated subquery references the outer row and is logically evaluated per row — often better rewritten as a join or window function.

#### Q. IN vs EXISTS?
EXISTS short-circuits on first match (good for large inner sets); IN is fine for small lists; NOT EXISTS beats NOT IN because NOT IN breaks on NULLs.

#### Q. View vs materialized view?
View re-runs its query (fresh, no storage); materialized view stores results (fast, must refresh, can be stale).

#### Q. char vs varchar?
`CHAR(n)` is fixed-length (padded), `VARCHAR(n)` is variable-length (stores actual length). Use CHAR for truly fixed codes, VARCHAR otherwise.

#### Q. What is the CAP theorem (distributed DBs)?
In a partitioned distributed system you can guarantee at most two of Consistency, Availability, Partition-tolerance. Since partitions happen, you effectively choose consistency or availability during a partition.

#### Q. OLTP vs OLAP?
OLTP = many small transactional operations (normalized, low latency writes). OLAP = analytical, read-heavy aggregation over large data (often denormalized/columnar).

---

# Last-Minute Revision Checklist

**Theory (where non-degree devs lose marks — nail these):**
- [ ] DBMS vs RDBMS; why relational over files.
- [ ] Keys: super/candidate/primary/unique/composite/foreign; PK vs unique.
- [ ] Normalization 1NF→BCNF with the *why* (anomalies); when to denormalize.
- [ ] Integrity: entity / referential / domain.
- [ ] SQL execution order → explains WHERE vs HAVING and alias rules.

**Queries:**
- [ ] All joins + the LEFT-JOIN filter-in-WHERE-vs-ON trap + anti-join.
- [ ] UNION vs UNION ALL; COUNT(*) vs COUNT(col); NULL 3-valued logic + NOT IN trap.
- [ ] Subquery ↔ join ↔ window rewrite drill ("above dept average") + how to narrate the optimization.
- [ ] IN vs EXISTS; NOT EXISTS for "no match".
- [ ] Nth highest (3 ways), top-N per group, duplicates, delete duplicates, gaps & islands, running total, LAG/LEAD, pivot.

**Indexing & performance:**
- [ ] B+ tree; clustered vs non-clustered; leftmost-prefix; covering index.
- [ ] When an index is NOT used (function on column, leading %, low selectivity...).
- [ ] Read EXPLAIN; sargable predicates; keyset vs offset pagination; N+1.

**Transactions:**
- [ ] ACID (+ what breaks without each).
- [ ] Read phenomena + isolation levels + defaults (Postgres READ COMMITTED, MySQL REPEATABLE READ).
- [ ] Optimistic vs pessimistic locking; MVCC; deadlock cause/handling.

**Advanced:**
- [ ] ROW_NUMBER vs RANK vs DENSE_RANK; window vs GROUP BY.
- [ ] CTE + recursive CTE; view vs materialized view; proc vs function vs trigger.

**Delivery tips:**
- [ ] For any query: state the approach, then optimize, then mention the index that helps.
- [ ] Always say "I'd confirm with EXPLAIN" — shows you reason about plans, not memorize.
- [ ] Know the *why* behind each rule — that's what separates a 3-year dev from a fresher.

**You've got the theory *and* the query craft now. Reason out loud, and you'll do great.**
