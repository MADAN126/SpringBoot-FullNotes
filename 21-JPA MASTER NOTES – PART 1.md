# JPA MASTER NOTES – PART 1
## Foundations, Entities, ID Generation, and Spring Boot Startup Flow
### (Pages 1–25)

---

# 1. Introduction to JPA

## What is JPA?

JPA stands for:

```text
Java Persistence API
```

It is a specification that defines how Java objects should be stored and retrieved from relational databases.

JPA itself does not perform database operations.

It only provides rules and interfaces.

Example:

```text
JPA → Specification

Hibernate → JPA Implementation

Spring Data JPA → Wrapper around Hibernate
```

Flow:

```text
Application
     ↓
Spring Data JPA
     ↓
Hibernate
     ↓
JDBC
     ↓
Database
```

---

## Why JPA?

Without JPA:

```java
Connection con = DriverManager.getConnection(...);

PreparedStatement ps =
con.prepareStatement(
"INSERT INTO EMP VALUES (?,?)");

ps.executeUpdate();
```

Problems:

- Too much boilerplate code
- Manual ResultSet handling
- SQL everywhere
- Difficult maintenance

JPA solves this by allowing:

```java
repo.save(emp);
```

instead of

```sql
INSERT INTO emp_details...
```

---

# 2. JPA Architecture

JPA internally works as:

```text
Spring Boot
    ↓
Spring Data JPA
    ↓
Hibernate
    ↓
JDBC
    ↓
Database
```

## Responsibilities

### Spring Data JPA

Provides:

```text
save()

findAll()

findById()

delete()
```

without implementation.

---

### Hibernate

Responsible for:

```text
Object → SQL conversion

Relationship handling

Caching

Dirty checking
```

---

### JDBC

Responsible for:

```text
Sending SQL to Database.
```

---

# 3. Hibernate vs JPA vs Spring Data JPA

| Feature | JPA | Hibernate | Spring Data JPA |
|----------|-----|------------|-----------------|
| Type | Specification | Implementation | Abstraction |
| Provides SQL generation | No | Yes | Through Hibernate |
| Repository support | No | No | Yes |
| CRUD methods | No | Partial | Yes |
| Query derivation | No | No | Yes |

---

# 4. Entity Lifecycle

Every entity passes through states.

```text
NEW
 ↓
MANAGED
 ↓
DETACHED
 ↓
REMOVED
```

---

## NEW State

Object created.

Example:

```java
EmployeeDetails emp =
new EmployeeDetails();
```

Not connected to DB.

---

## MANAGED State

Example:

```java
repo.save(emp);
```

Hibernate tracks object.

---

## DETACHED State

Example:

```text
Persistence Context Closed
```

Object exists.

Hibernate stops tracking.

---

## REMOVED State

Example:

```java
repo.delete(emp);
```

Scheduled for deletion.

---

# 5. @Entity Annotation

Marks class as JPA entity.

Example:

```java
@Entity
public class EmployeeDetails {

}
```

Meaning:

```text
Java Class
    ↓
Database Table
```

Without @Entity:

```text
Hibernate ignores class.
```

---

# 6. @Table Annotation

Specifies table name.

Example:

```java
@Table(name="emp_details")
```

Without it:

```text
Entity Name = Table Name
```

With it:

```text
EmployeeDetails
        ↓
emp_details
```

---

# 7. EmployeeDetails Entity

Example:

```java
@Entity
@Table(name="emp_details")
public class EmployeeDetails {

}
```

Represents:

```text
EMP_DETAILS TABLE
```

Fields:

| Field | Column |
|---------|----------|
| employeeId | emp_id |
| name | name |
| age | age |
| salary | salary |
| city | city |
| gender | gender |
| country | country |

---

# 8. AddrDetails Entity

Example:

```java
@Entity
@Table(name="Addr")
public class AddrDetails {

}
```

Represents:

```text
ADDR TABLE
```

Fields:

| Field | Column |
|--------|---------|
| id | id |
| location | location |
| city | city |
| country | country |

---

# 9. Department Entity

Example:

```java
@Entity
@Table(name="Department")
public class Department {

}
```

Represents:

```text
DEPARTMENT TABLE
```

Fields:

| Field | Meaning |
|---------|-----------|
| deptId | Primary Key |
| deptName | Department Name |

---

# 10. Parking Entity

Example:

```java
@Entity
@Table(name="Parking")
public class Parking {

}
```

Represents:

```text
PARKING TABLE
```

Fields:

| Field | Meaning |
|---------|----------|
| id | Primary Key |
| isCar | Car/Bike |
| floorNo | Floor |
| monthlySub | Subscription |

---

# 11. @Id Annotation

Defines primary key.

Example:

```java
@Id
private long employeeId;
```

Meaning:

```text
employeeId
     ↓
Primary Key
```

SQL Equivalent:

```sql
PRIMARY KEY(emp_id)
```

---

# 12. @Column Annotation

Maps field to column.

Example:

```java
@Column(name="emp_id")
private long employeeId;
```

Meaning:

```text
Java Field
employeeId

↓

Database Column
emp_id
```

---

# 13. Primary Keys

Primary Key means:

```text
Unique Identifier
```

Properties:

- Unique
- Not Null
- Identifies each row

Example:

```text
101
102
103
```

No duplicates allowed.

---

# 14. @GeneratedValue

Used to generate IDs automatically.

Example:

```java
@GeneratedValue(
strategy = GenerationType.SEQUENCE,
generator = "addr_id"
)
```

Meaning:

```text
Do not manually assign IDs.
```

Hibernate generates them.

---

# 15. Generation Strategies

JPA supports four strategies.

---

## AUTO

Example:

```java
GenerationType.AUTO
```

Meaning:

```text
Database decides strategy.
```

---

## IDENTITY

Example:

```java
GenerationType.IDENTITY
```

Uses:

```sql
AUTO_INCREMENT
```

Example:

```text
1
2
3
4
```

---

## SEQUENCE

Example:

```java
GenerationType.SEQUENCE
```

Uses sequence objects.

Preferred in Oracle.

---

## TABLE

Example:

```java
GenerationType.TABLE
```

Uses separate table.

Less efficient.

---

# 16. Sequence Generator

Example:

```java
@SequenceGenerator(
name="addr_id",
sequenceName="addr_id",
allocationSize=1,
initialValue=1
)
```

Meaning:

```text
Sequence Name : addr_id

Starts At : 1

Increment : 1
```

Flow:

```text
Insert Entity
     ↓
Ask Sequence
     ↓
Get Next Value
     ↓
Assign ID
```

---

# 17. Parking Sequence

Example:

```java
@SequenceGenerator(
name="parking_id",
sequenceName="parking_id",
initialValue=100,
allocationSize=10
)
```

Meaning:

```text
Start : 100

Increment : 10
```

Generated IDs:

```text
100
110
120
130
...
```

---

# 18. Lombok Annotations

Your project uses:

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
```

---

## @Data

Generates:

```text
Getters

Setters

equals()

hashCode()

toString()
```

---

## @NoArgsConstructor

Generates:

```java
EmployeeDetails() {

}
```

Required by Hibernate.

---

## @AllArgsConstructor

Generates constructor.

Example:

```java
EmployeeDetails(
all fields
)
```

---

# 19. equals(), hashCode(), toString()

Used for object comparison.

Example:

```java
emp1.equals(emp2);
```

Determines:

```text
Same Object?
```

---

## hashCode()

Used in:

```text
HashMap

HashSet
```

---

## toString()

Used for printing.

Example:

```java
System.out.println(emp);
```

Output:

```text
EmployeeDetails[
employeeId=101,
name=Ravi,
...
]
```

---

# 20. Spring Boot Startup

Example:

```java
SpringApplication.run(
EmpDetailsApplication.class,
args
);
```

Flow:

```text
Start Application
      ↓
Create ApplicationContext
      ↓
Scan Components
      ↓
Create Beans
      ↓
Configure JPA
      ↓
Connect Database
      ↓
Application Ready
```

---

# 21. ConfigurableApplicationContext

Example:

```java
ConfigurableApplicationContext
container =
SpringApplication.run(...);
```

Meaning:

```text
Spring IOC Container
```

Responsibilities:

```text
Store Beans

Manage Lifecycle

Dependency Injection

Bean Retrieval
```

---

# 22. getBean()

Example:

```java
Operations op =
container.getBean("operations",
Operations.class);
```

Meaning:

```text
Ask Spring

↓

Give Bean Object
```

You never use:

```java
new Operations();
```

Spring creates it.

---

# 23. Operations Bean Flow

Example:

```java
op.AddUserWithMultipleParking();
```

Flow:

```text
Spring Starts
      ↓
Get Operations Bean
      ↓
Execute JPA Logic
      ↓
Repositories Called
      ↓
Hibernate Generates SQL
      ↓
Database Updated
```

---

# 24. Internal Startup Flow

```text
main()
   ↓
SpringApplication.run()
   ↓
Create IOC Container
   ↓
Component Scan
   ↓
Create Repositories
   ↓
Configure EntityManager
   ↓
Initialize Hibernate
   ↓
Connect Database
   ↓
Application Ready
```

---

# 25. Final Mental Picture

```text
Java Objects
      ↓
@Entity Mapping
      ↓
Hibernate Converts
      ↓
SQL Queries
      ↓
Database Tables
```

```text
EmployeeDetails
       ↓
emp_details

AddrDetails
       ↓
Addr

Department
       ↓
Department

Parking
       ↓
Parking
```

```text
Spring Boot
      ↓
Spring Data JPA
      ↓
Hibernate
      ↓
JDBC
      ↓
Database
```

## End of Part 1
(Foundations, Entities, ID Generation & Startup Flow)
