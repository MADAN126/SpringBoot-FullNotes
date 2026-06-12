# Spring Data JPA Notes (Based on Your Project)

---

# 1. Project Structure

```text
EmpDetailsApplication
        ↓
Spring Boot starts
        ↓
Entity Classes
    ├─ EmployeeDetails
    ├─ AddrDetails
    ├─ Parking
    └─ Department
        ↓
Repository Interfaces
    ├─ Repo
    ├─ ParkingRepo
    └─ DeptRepo
        ↓
Operations Class
        ↓
Database
```

---

# 2. Main Class

```java
@SpringBootApplication
public class EmpDetailsApplication {

    public static void main(String[] args) {

        ConfigurableApplicationContext container =
                SpringApplication.run(EmpDetailsApplication.class, args);

        Operations op1 = container.getBean("operations", Operations.class);

        op1.AddUserWithMultipleParking();
    }
}
```

## Internal Flow

```text
main()
   ↓
SpringApplication.run()
   ↓
Auto Configuration
   ↓
Entity Scanning
   ↓
Repository Creation
   ↓
Bean Creation
   ↓
Operations Bean Retrieved
   ↓
Business Method Executes
```

---

# 3. EmployeeDetails Entity

```java
@Entity
@Table(name="emp_details")
public class EmployeeDetails
```

Represents Employee Table.

---

## Columns

```java
@Id
@Column(name="emp_id")
private long employeeId;
```

Primary Key.

---

```java
@Column(name="name")
private String name;
```

Employee Name.

---

```java
private int age;

private float salary;

private String city;

private String gender;

private String country;
```

Simple Columns.

---

# Employee Table

```text
emp_details

emp_id
name
age
salary
city
gender
country
addr_id
dept_emp_id
```

---

# 4. One-To-One Mapping

Employee ↔ Address

```java
@OneToOne(cascade = CascadeType.ALL)
@JoinColumn(name="addr_id")
private AddrDetails addr;
```

---

## Meaning

One employee has one address.

One address belongs to one employee.

---

## Join Column

```text
emp_details
-----------
addr_id (FK)

        ↓

Addr
----
id (PK)
```

---

## Cascade.ALL

```text
Save Employee
      ↓
Save Address

Delete Employee
      ↓
Delete Address
```

---

# 5. AddrDetails Entity

```java
@Entity
@Table(name="Addr")
public class AddrDetails
```

Represents Address Table.

---

## Fields

```java
@Id
@GeneratedValue(...)
private int id;

private String location;

private String city;

private String country;
```

---

# Sequence Generator

```java
@SequenceGenerator(
        name="addr_id",
        sequenceName="addr_id",
        allocationSize=1,
        initialValue=1
)
@GeneratedValue(
        strategy=GenerationType.SEQUENCE,
        generator="addr_id"
)
```

---

## Internal Flow

```text
Insert Address
      ↓
Use Database Sequence
      ↓
Generate ID
      ↓
Store Record
```

Example:

```text
1
2
3
4
...
```

---

# 6. One-To-Many Mapping

Employee → Parking

```java
@OneToMany(
        cascade=CascadeType.ALL,
        fetch=FetchType.EAGER
)
@JoinColumn(name="emp_id")
private List<Parking> Parking;
```

---

## Meaning

One Employee

can have

Many Parking Slots.

---

## Database

```text
Employee
   1
   ↓
Parking
-------
101
102
103
```

---

## Table Structure

```text
emp_details
------------
emp_id

Parking
--------
id
emp_id (FK)
iscar
floorno
monthlySub
```

---

# Internal Flow

```text
Save Employee
      ↓
Save Parking 1
Save Parking 2
Save Parking 3
```

Because:

```java
CascadeType.ALL
```

---

# FetchType.EAGER

Means:

```text
Get Employee
      ↓
Immediately Fetch Parking
```

SQL Idea:

```text
SELECT Employee
SELECT Parking
```

Automatically.

---

# 7. Many-To-One Mapping

Employee → Department

```java
@ManyToOne(fetch = FetchType.EAGER)

@JoinColumn(name="dept_emp_id")

private Department department;
```

---

## Meaning

Many Employees

belong to

One Department.

---

Example

```text
IT Department
    ↓
Emp1
Emp2
Emp3
Emp4
```

---

# Database

```text
Department
----------
deptId
deptName

emp_details
-----------
dept_emp_id (FK)
```

---

# Internal Flow

```text
Employee Saved
      ↓
Department ID Stored
```

---

# 8. Department Entity

```java
@Entity
@Table(name="Department")
public class Department
```

---

Fields

```java
@Id
private int deptId;

private String deptName;
```

Example:

```text
1 IT
2 HR
3 Finance
```

---

# 9. Parking Entity

```java
@Entity
@Table(name="Parking")
```

---

Fields

```java
@Id
@GeneratedValue(...)
private int id;

private boolean isCar;

private int floorNo;

private int monthlySub;
```

---

# Parking Sequence

```java
@SequenceGenerator(
    name="parking_id",
    sequenceName="parking_id",
    initialValue=100,
    allocationSize=10
)
```

---

Generated IDs

```text
100
110
120
130
...
```

Because:

```text
allocationSize = 10
```

---

# Parking Many-To-One

```java
@ManyToOne

@JoinColumn(name="emp_id")

private EmployeeDetails employee;
```

---

Meaning

```text
Parking
    ↓
knows
    ↓
Employee
```

Bidirectional Relationship.

---

# Relationship Summary

```text
Employee
   │
   ├──── OneToOne ────► Address
   │
   ├──── OneToMany ───► Parking
   │
   └──── ManyToOne ───► Department
```

---

# Database Diagram

```text
Department
(deptId PK)
      ▲
      │ Many Employees
      │
emp_details
(emp_id PK)
      │
      ├──── addr_id FK ───► Addr(id PK)
      │
      └──── emp_id FK ───► Parking(id PK)
```

---

# 10. Repository Layer

Spring Data creates implementation automatically.

---

## Employee Repository

```java
public interface Repo
extends JpaRepository<EmployeeDetails,Long>
```

---

Means

```text
Entity     : EmployeeDetails

Primary Key: Long
```

---

# JpaRepository Gives

```java
save()

findById()

findAll()

delete()

count()

existsById()

saveAll()

deleteById()
```

Automatically.

---

# 11. JPQL Query

```java
@Query(
value="SELECT E FROM EmployeeDetails E",
nativeQuery=false
)
List<EmployeeDetails> getAllRecords();
```

---

Meaning

JPQL uses:

```text
Entity Name

NOT

Table Name
```

Example

```text
EmployeeDetails

NOT

emp_details
```

---

# 12. Native Query

```java
@Query(
value=
"SELECT * FROM emp_details
 WHERE COUNTRY=:COUNTRY
 AND AGE<:AGE",

nativeQuery=true
)
```

---

Meaning

Uses actual SQL.

Uses:

```text
Table Names

Column Names
```

---

# Named Parameters

```java
@Param("COUNTRY")

@Param("AGE")
```

Example:

```java
repo.getEmpByCountryAndAge("India",30);
```

---

# Positional Parameters

```java
@Query(
value=
"SELECT * FROM emp_details
 WHERE GENDER=?1
 AND AGE=?2",

nativeQuery=true
)
```

Example:

```java
repo.getEmpByGenderAndAge("Male",25);
```

---

# 13. Derived Query Methods

Spring generates SQL automatically.

---

```java
findByCity(String city)
```

Becomes:

```sql
SELECT *
FROM emp_details
WHERE city=?
```

---

```java
findByGender(String gender)
```

Becomes:

```sql
SELECT *
FROM emp_details
WHERE gender=?
```

---

```java
findBySalary(int salary)
```

Becomes:

```sql
SELECT *
FROM emp_details
WHERE salary=?
```

---

```java
findByGenderAndCountry(
        String gender,
        String country
)
```

Becomes:

```sql
SELECT *
FROM emp_details
WHERE gender=?
AND country=?
```

---

# Delete Queries

```java
int deleteByCity(String city);
```

Returns:

```text
Number of rows deleted.
```

Example:

```java
int count = repo.deleteByCity("Chennai");
```

---

# 14. Fetch Types

## EAGER

```text
Load immediately.
```

Example

```java
@ManyToOne(fetch=EAGER)
```

Flow:

```text
Get Employee
      ↓
Get Department Immediately
```

---

## LAZY

```text
Load only when needed.
```

Flow:

```text
Get Employee
      ↓
Department NOT loaded
      ↓
Call getDepartment()
      ↓
Department loaded
```

---

# 15. Cascade Types

```java
CascadeType.ALL
```

Means:

```text
PERSIST
MERGE
REMOVE
REFRESH
DETACH
```

All operations propagate.

---

Example

```text
Save Employee
      ↓
Save Address

Delete Employee
      ↓
Delete Address
```

---

# 16. Complete Save Flow

Suppose:

```java
Employee
    +
Address
    +
3 Parking Slots
    +
Department
```

are connected.

---

Spring Flow

```text
save(employee)
      ↓
Employee Inserted
      ↓
Address Inserted
      ↓
Parking 1 Inserted
Parking 2 Inserted
Parking 3 Inserted
      ↓
Department FK Stored
      ↓
Commit Transaction
```

---

# Final Mental Picture

```text
Spring Boot Starts
        ↓
Entities Scanned
        ↓
Repositories Created
        ↓
Beans Created
        ↓
Operations Bean Executes
        ↓
save(employee)
        ↓
JPA Builds SQL
        ↓
Relationships Managed
        ↓
Database Updated
```

---

# Exam One-Line Revision

```text
@Entity          → Maps class to table
@Table           → Specifies table name
@Id              → Primary key
@GeneratedValue  → Auto-generate ID
@OneToOne        → One ↔ One
@OneToMany       → One ↔ Many
@ManyToOne       → Many ↔ One
@JoinColumn      → Foreign key column
Cascade.ALL      → Propagate all operations
EAGER            → Load immediately
JpaRepository    → Ready-made CRUD
JPQL             → Query using entity names
Native Query     → Query using SQL/table names
Derived Methods  → SQL generated from method names
```
