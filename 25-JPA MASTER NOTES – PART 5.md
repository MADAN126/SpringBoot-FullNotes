# Part 5 → JPA_MASTER_NOTES.md (Advanced Concepts, Fetching, Cascading & Interview Points)

---

# 32. FetchType in JPA

FetchType determines:

```text
WHEN related entities should be loaded.
```

There are two types:

```text
1. EAGER
2. LAZY
```

---

# FetchType.EAGER

Meaning:

```text
Load parent and child immediately.
```

---

Example

```java
@OneToMany(fetch = FetchType.EAGER)
private List<Parking> parking;
```

---

Suppose:

Employee:

```text
101
Madhan
```

Parking:

```text
P1
P2
P3
```

---

When executing:

```java
repo.findById(101L);
```

Internally:

```text
Employee loaded
        ↓
Parking loaded immediately
        ↓
Return complete object graph
```

---

Visual Flow

```text
Employee
    ↓
Parking
    ↓
Returned Together
```

---

Generated SQL

```sql
SELECT *
FROM emp_details;

SELECT *
FROM parking
WHERE emp_id=?;
```

---

# Advantages

```text
Easy to use

No LazyInitializationException
```

---

# Disadvantages

```text
Extra SQL

Performance overhead
```

---

# FetchType.LAZY

Meaning:

```text
Load child only when needed.
```

---

Example

```java
@OneToMany(fetch = FetchType.LAZY)
private List<Parking> parking;
```

---

Execution

```java
Employee emp=
repo.findById(101L).get();
```

Only Employee loaded.

---

SQL

```sql
SELECT *
FROM emp_details
WHERE emp_id=?;
```

Parking NOT loaded.

---

Later:

```java
emp.getParking();
```

Now SQL executes:

```sql
SELECT *
FROM parking
WHERE emp_id=?;
```

---

Visual Flow

```text
Employee Loaded
      ↓
Parking Deferred
      ↓
getParking()
      ↓
Parking Loaded
```

---

# Comparison

| Feature | EAGER | LAZY |
|----------|--------|-------|
| Child loaded immediately | Yes | No |
| Performance | Lower | Better |
| SQL generated | More | Less |
| Default for OneToMany | No | Yes |
| Default for ManyToOne | Yes | No |

---

# 33. Cascade Operations

Cascade means:

```text
Apply parent operations to child entities.
```

---

Example

```java
@OneToOne(cascade=CascadeType.ALL)
private AddrDetails addr;
```

---

Without Cascade

```text
Save Employee
      ↓
Address NOT saved
```

---

With Cascade

```text
Save Employee
      ↓
Address automatically saved
```

---

# Cascade Types

```text
ALL

PERSIST

MERGE

REMOVE

REFRESH

DETACH
```

---

# CascadeType.ALL

Meaning:

```text
Apply all operations.
```

---

Operations cascaded:

```text
save

update

delete

merge

refresh

detach
```

---

Example

```java
@OneToMany(
cascade=CascadeType.ALL)
private List<Parking> parking;
```

---

# Save Flow

```text
save(Employee)
      ↓
save(Address)
      ↓
save(Parking)
```

---

# Delete Flow

```text
delete(Employee)
      ↓
delete(Address)
      ↓
delete(Parking)
```

---

# CascadeType.PERSIST

Meaning:

```text
Only save cascades.
```

---

Flow

```text
save(Employee)
      ↓
save(Child)
```

Delete does NOT cascade.

---

# CascadeType.MERGE

Meaning:

```text
Update cascades.
```

---

Flow

```text
update(Employee)
       ↓
update(Child)
```

---

# CascadeType.REMOVE

Meaning:

```text
Delete cascades.
```

---

Flow

```text
delete(Employee)
       ↓
delete(Child)
```

---

# 34. Bidirectional vs Unidirectional Relationships

---

# Unidirectional

Only one side knows relationship.

Example:

Employee

```java
@OneToMany
private List<Parking> parking;
```

Parking:

```java
No Employee reference.
```

---

Flow

```text
Employee
     ↓
Parking
```

Only.

---

# Bidirectional

Both sides know relationship.

Employee:

```java
@OneToMany
private List<Parking> parking;
```

Parking:

```java
@ManyToOne
private Employee employee;
```

---

Flow

```text
Employee
    ↔
Parking
```

---

Advantages

```text
Navigation possible both ways.
```

---

Disadvantages

```text
Can create recursion issues.
```

Example:

```text
Employee
  ↓
Parking
  ↓
Employee
  ↓
Parking
...
```

---

# 35. Owning Side

Very Important Interview Topic.

---

Meaning

```text
Side controlling foreign key.
```

---

Example

Parking:

```java
@ManyToOne
@JoinColumn(name="emp_id")
private Employee employee;
```

---

Employee:

```java
@OneToMany
private List<Parking> parking;
```

---

Who owns relationship?

```text
Parking
```

because:

```java
@JoinColumn
```

exists there.

---

Visual

```text
Parking
   ↓
Foreign Key Updated
```

---

# Rule

```text
@JoinColumn Side

=

Owning Side
```

---

# 36. N+1 Problem

Common Hibernate Problem.

---

Suppose:

10 Employees.

Each Employee:

```text
3 Parking records.
```

---

Execution:

```java
repo.findAll();
```

---

SQL

```sql
SELECT *
FROM emp_details;
```

1 query.

Then:

```sql
SELECT *
FROM parking
WHERE emp_id=1;

SELECT *
FROM parking
WHERE emp_id=2;

SELECT *
FROM parking
WHERE emp_id=3;

...
```

10 more queries.

---

Total:

```text
11 Queries
```

---

Formula

```text
1 + N
```

Hence:

```text
N+1 Problem
```

---

Disadvantages

```text
Slow Performance

Database Overload
```

---

Solutions

```text
JOIN FETCH

EntityGraph

Batch Fetching
```

---

# 37. Entity Lifecycle States

JPA Entities have four states.

---

# New (Transient)

```java
Employee e=
new Employee();
```

Not managed.

Not in database.

---

Flow

```text
Java Object

Only JVM
```

---

# Managed (Persistent)

Example

```java
entityManager.persist(e);
```

---

Flow

```text
EntityManager manages object.
```

---

Changes tracked.

---

# Detached

Example

```java
entityManager.detach(e);
```

---

Meaning

```text
Entity exists

But no longer managed.
```

---

Changes NOT tracked.

---

# Removed

Example

```java
entityManager.remove(e);
```

---

Meaning

```text
Scheduled for deletion.
```

---

Visual Lifecycle

```text
Transient
    ↓
persist()
    ↓
Managed
    ↓
detach()
    ↓
Detached
    ↓
remove()
    ↓
Removed
```

---

# 38. Common Interview Questions

---

Q1. Difference between JPA and Hibernate?

Answer:

```text
JPA

→ Specification

Hibernate

→ Implementation of JPA
```

---

Q2. What is EntityManager?

Answer:

```text
Core JPA Interface.

Handles persistence operations.
```

---

Q3. What is Cascade?

Answer:

```text
Propagation of parent operations
to child entities.
```

---

Q4. What is FetchType?

Answer:

```text
Determines when child entities load.
```

---

Q5. Default Fetch Types?

Answer:

```text
@OneToMany  → LAZY

@ManyToOne → EAGER

@OneToOne  → EAGER

@ManyToMany → LAZY
```

---

Q6. What is Persistence Context?

Answer:

```text
First Level Cache
containing managed entities.
```

---

Q7. What is Dirty Checking?

Answer:

```text
Hibernate automatically detects
entity changes and updates DB.
```

---

# Final Mental Picture

```text
Entity Created
      ↓
Managed by EntityManager
      ↓
Relationships Connected
      ↓
Cascade Applied
      ↓
Fetch Strategy Used
      ↓
Hibernate Generates SQL
      ↓
Database Updated
```

---

## End of Part 5

Covered Topics:

✔ FetchType (EAGER vs LAZY)

✔ Cascade Operations

✔ CascadeType.ALL

✔ Bidirectional Relationships

✔ Owning Side

✔ N+1 Problem

✔ Entity Lifecycle States

✔ Persistence Context Concepts

✔ Advanced Interview Questions
