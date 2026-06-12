# JPA MASTER NOTES – PART 2
## Relationships, Cascading, Fetch Types, and Mapping Internals
### (Pages 26–50)

---

# 26. JPA Relationships

Entities rarely exist independently.

Example:

```text
Employee has an Address.
Employee has Parking records.
Employee belongs to a Department.
```

JPA provides relationship annotations to model these connections.

Relationship Types:

```text
@OneToOne
@OneToMany
@ManyToOne
@ManyToMany
```

Your project uses:

```text
@OneToOne
@OneToMany
@ManyToOne
```

---

# 27. One-to-One Mapping

Definition:

```text
One record in Entity A
        ↓
maps to
        ↓
One record in Entity B
```

Example:

```text
One Employee
        ↓
One Address
```

Diagram:

```text
Employee ───────── Address
     1                 1
```

---

# 28. Employee ↔ Address Mapping

Code:

```java
@OneToOne(cascade = CascadeType.ALL)
@JoinColumn(name = "addr_id")
private AddrDetails addr;
```

Meaning:

```text
Employee owns Address.

Foreign key stored in Employee table.
```

Database:

```text
EMP_DETAILS
------------
emp_id
addr_id (FK)

ADDR
-----
id
location
city
country
```

---

# 29. @OneToOne Annotation

Purpose:

```text
Defines a one-to-one relationship.
```

Syntax:

```java
@OneToOne
private Address address;
```

Flow:

```text
Employee Object
       ↓
Contains Address Object
       ↓
Hibernate creates FK
```

---

# 30. @JoinColumn

Purpose:

```text
Defines foreign key column.
```

Example:

```java
@JoinColumn(name="addr_id")
```

Meaning:

```text
addr_id in Employee table
points to
Addr.id
```

SQL Equivalent:

```sql
FOREIGN KEY(addr_id)
REFERENCES Addr(id)
```

---

# 31. Cascade Operations

Cascade means:

```text
Parent operation
       ↓
Automatically applied
       ↓
Child entity
```

Example:

```java
@OneToOne(cascade = CascadeType.ALL)
```

Saving Employee:

```text
Save Employee
      ↓
Save Address
```

Deleting Employee:

```text
Delete Employee
      ↓
Delete Address
```

---

# 32. Cascade Types

JPA provides:

```text
PERSIST
MERGE
REMOVE
REFRESH
DETACH
ALL
```

---

## PERSIST

Example:

```java
cascade = CascadeType.PERSIST
```

Meaning:

```text
Save Parent
     ↓
Save Child
```

---

## MERGE

Meaning:

```text
Update Parent
      ↓
Update Child
```

---

## REMOVE

Meaning:

```text
Delete Parent
      ↓
Delete Child
```

---

## REFRESH

Meaning:

```text
Reload entity from database.
```

---

## DETACH

Meaning:

```text
Remove entity from persistence context.
```

---

## ALL

Meaning:

```text
Apply every cascade operation.
```

---

# 33. One-to-Many Mapping

Definition:

```text
One Parent
      ↓
Many Children
```

Example:

```text
One Employee
      ↓
Many Parking Records
```

Diagram:

```text
Employee
   |
   |── Parking
   |
   |── Parking
   |
   |── Parking
```

---

# 34. Employee ↔ Parking Mapping

Code:

```java
@OneToMany(
    cascade = CascadeType.ALL,
    fetch = FetchType.EAGER
)
@JoinColumn(name="emp_id")
private List<Parking> parking;
```

Meaning:

```text
Employee owns multiple Parking objects.
```

---

# 35. @OneToMany Annotation

Purpose:

```text
Represents collection relationships.
```

Example:

```java
@OneToMany
List<Parking> parking;
```

Meaning:

```text
One Employee
       ↓
List of Parking
```

---

# 36. Unidirectional Relationship

Definition:

```text
Only one side knows the relationship.
```

Example:

```java
Employee
    ↓
Parking
```

Parking does not know Employee.

Diagram:

```text
Employee → Parking
```

---

# 37. Bidirectional Relationship

Definition:

```text
Both entities know each other.
```

Example:

```java
Employee
    ↓
Parking

Parking
    ↑
Employee
```

Diagram:

```text
Employee ↔ Parking
```

---

# 38. Parking Entity Deep Dive

Code:

```java
@Entity
@Table(name="Parking")
```

Fields:

```java
id
isCar
floorNo
monthlySub
employee
```

Meaning:

```text
Represents parking details of employees.
```

---

# 39. Parking ↔ Employee Mapping

Code:

```java
@ManyToOne
@JoinColumn(name="emp_id")
private EmployeeDetails employee;
```

Meaning:

```text
Many Parking rows
      ↓
belong to
      ↓
One Employee
```

Diagram:

```text
Parking
Parking
Parking
     ↓
Employee
```

---

# 40. Many-to-One Mapping

Definition:

```text
Many Child Records
       ↓
One Parent Record
```

Example:

```text
Many Employees
       ↓
One Department
```

---

# 41. Employee ↔ Department Mapping

Code:

```java
@ManyToOne(fetch = FetchType.EAGER)
@JoinColumn(name="dept_emp_id")
private Department department;
```

Meaning:

```text
Employee belongs to Department.
```

Database:

```text
EMP_DETAILS
------------
dept_emp_id

DEPARTMENT
----------
deptId
deptName
```

---

# 42. Department Entity Deep Dive

Fields:

```java
deptId
deptName
```

Example:

```java
Department dept =
new Department(10,"HR");
```

Meaning:

```text
Department ID : 10
Department : HR
```

---

# 43. Fetch Types

Fetch type determines:

```text
WHEN related entities load.
```

Two Types:

```text
EAGER
LAZY
```

---

# 44. FetchType.EAGER

Meaning:

```text
Load immediately.
```

Example:

```java
@ManyToOne(fetch = FetchType.EAGER)
```

Flow:

```text
Load Employee
      ↓
Load Department
```

SQL:

```sql
SELECT *
FROM emp_details;

SELECT *
FROM department;
```

---

# 45. FetchType.LAZY

Meaning:

```text
Load only when needed.
```

Flow:

```text
Load Employee
      ↓
Department NOT loaded

department.getName()
      ↓
Load Department
```

Advantage:

```text
Improves performance.
```

---

# 46. SQL Generated for Relationships

Saving Employee:

```java
repo.save(emp);
```

Example SQL:

```sql
INSERT INTO Addr...

INSERT INTO Department...

INSERT INTO emp_details...

INSERT INTO Parking...
```

Hibernate manages order automatically.

---

# 47. Parent–Child Relationships

Example:

```text
Employee
     ↓
Parking
```

Employee:

```text
Parent
```

Parking:

```text
Child
```

Rules:

```text
Parent owns lifecycle.

Cascade affects children.
```

---

# 48. Cascade vs Fetch

Cascade controls:

```text
WHAT operations propagate.
```

Example:

```text
Save
Delete
Merge
```

Fetch controls:

```text
WHEN related entities load.
```

Example:

```text
Immediate

or

On-demand
```

Comparison:

| Feature | Cascade | Fetch |
|----------|-----------|--------|
| Controls | Operations | Loading |
| Examples | Save/Delete | EAGER/LAZY |
| Purpose | Lifecycle | Performance |

---

# 49. Common Mapping Mistakes

Mistake 1:

```text
No @JoinColumn
```

Result:

```text
Unexpected join table.
```

---

Mistake 2:

```text
Using EAGER everywhere.
```

Result:

```text
Performance issues.
```

---

Mistake 3:

```text
No Cascade.
```

Result:

```text
Child entities not saved.
```

---

Mistake 4:

```text
Bidirectional toString()
```

Result:

```text
Infinite recursion.
```

---

# 50. Internal Working of JPA Relationships

Saving Employee with Address and Parking:

```text
repo.save(employee)
        ↓
Hibernate inspects Employee
        ↓
Finds Address
        ↓
Cascade Save Address
        ↓
Finds Parking List
        ↓
Cascade Save Parking
        ↓
Generates Foreign Keys
        ↓
Executes SQL
        ↓
Transaction Commit
```

Final Mental Picture:

```text
Employee
    ↓
Address         (@OneToOne)

Employee
    ↓↓↓
Parking[]       (@OneToMany)

Employee
    ↓
Department      (@ManyToOne)
```

```text
Parent Entity
      ↓
Hibernate Traverses Graph
      ↓
Generates SQL
      ↓
Maintains Foreign Keys
      ↓
Stores Entire Object Graph
```

## End of Part 2
### (Relationships, Cascading, Fetch Types & Mapping Internals)
