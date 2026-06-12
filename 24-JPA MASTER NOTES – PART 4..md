# Part 4 → JPA_MASTER_NOTES.md (Application Flow, Spring Boot Startup & Complete Internal Architecture)

---

# 22. Spring Boot Main Class

Main Class:

```java
@SpringBootApplication
public class EmpDetailsApplication {

    public static void main(String[] args) {

        ConfigurableApplicationContext container =
                SpringApplication.run(
                        EmpDetailsApplication.class,
                        args);

        Operations op1 =
                container.getBean("operations",
                        Operations.class);

        op1.AddUserWithMultipleParking();
    }
}
```

---

# Why do we need Main Class?

Every Spring Boot application starts from:

```java
public static void main()
```

This acts as the entry point.

Without this:

```text
Spring Boot cannot start.
```

---

# SpringApplication.run()

This is one of the most important methods.

```java
SpringApplication.run(
        EmpDetailsApplication.class,
        args);
```

---

# What happens internally?

```text
main()
   ↓
SpringApplication.run()
   ↓
Create Spring Container
   ↓
Read Configurations
   ↓
Auto Configuration Starts
   ↓
Component Scanning Starts
   ↓
Repository Scanning Starts
   ↓
Entity Scanning Starts
   ↓
Hibernate Initialization
   ↓
Datasource Creation
   ↓
Transaction Manager Creation
   ↓
All Beans Created
   ↓
IOC Container Ready
   ↓
Application Starts
```

---

# Return Type

```java
ConfigurableApplicationContext
```

This represents:

```text
IOC Container
```

---

# Why store it?

Because we can do:

```java
container.getBean(...)
```

Example:

```java
Operations op =
        container.getBean(Operations.class);
```

---

# Internal Representation

```text
ConfigurableApplicationContext
        ↓
ApplicationContext
        ↓
BeanFactory
        ↓
IOC Container
```

---

# 23. Getting Bean from Container

Example:

```java
Operations op1 =
container.getBean(
        "operations",
        Operations.class);
```

---

# What happens internally?

```text
Search bean named "operations"
         ↓
Found Operations bean
         ↓
Return singleton instance
```

---

# Equivalent

Spring does:

```java
Operations op =
new Operations();
```

But:

```text
Spring manages lifecycle.
```

---

# Internal Flow

```text
getBean()
     ↓
Search BeanDefinition
     ↓
Locate Bean Instance
     ↓
Return Object
```

---

# 24. Calling Business Method

Example:

```java
op1.AddUserWithMultipleParking();
```

---

# Purpose

Usually used to perform:

```text
Insert Operations

Update Operations

Delete Operations

Testing Relationships
```

---

# Example Internal Flow

```text
AddUserWithMultipleParking()
           ↓
Create Employee
           ↓
Create Address
           ↓
Create Parking Objects
           ↓
Set Relationships
           ↓
repo.save(employee)
           ↓
Hibernate Executes SQL
```

---

# 25. Complete Save Flow

Suppose:

```java
repo.save(employee);
```

---

# Internally

```text
save()
    ↓
JpaRepository Proxy
    ↓
SimpleJpaRepository
    ↓
EntityManager.persist()
    ↓
Hibernate Session
    ↓
SQL Generation
    ↓
Database INSERT
```

---

# Visual Diagram

```text
repo.save(emp)
        ↓
JpaRepository
        ↓
SimpleJpaRepository
        ↓
EntityManager
        ↓
Hibernate
        ↓
JDBC
        ↓
Oracle/MySQL
```

---

# 26. Hibernate's Role

Hibernate is the JPA Provider.

JPA only defines rules.

Example:

```text
JPA says:
"save entity"

Hibernate says:
"I know HOW to save."
```

---

# Responsibilities of Hibernate

Hibernate performs:

```text
Object → SQL Conversion

Relationship Handling

Cascade Handling

Dirty Checking

Lazy Loading

Caching

Transaction Synchronization
```

---

# Example

You write:

```java
repo.save(emp);
```

Hibernate generates:

```sql
INSERT INTO emp_details(...)

INSERT INTO Addr(...)

INSERT INTO Parking(...)
```

---

# 27. Entity Manager

EntityManager is the heart of JPA.

It performs:

```text
persist()

merge()

remove()

find()

flush()

detach()
```

---

# Internal Hierarchy

```text
Application
    ↓
Repository
    ↓
EntityManager
    ↓
Hibernate Session
    ↓
Database
```

---

# persist()

Purpose:

```text
Insert New Entity
```

Example:

```java
entityManager.persist(emp);
```

Equivalent SQL:

```sql
INSERT INTO emp_details...
```

---

# find()

Purpose:

```text
Retrieve Entity
```

Example:

```java
entityManager.find(
        EmployeeDetails.class,
        101L);
```

Equivalent SQL:

```sql
SELECT *
FROM emp_details
WHERE emp_id=101;
```

---

# remove()

Purpose:

```text
Delete Entity
```

Example:

```java
entityManager.remove(emp);
```

Equivalent SQL:

```sql
DELETE
FROM emp_details
WHERE emp_id=?;
```

---

# merge()

Purpose:

```text
Update Detached Entity
```

Equivalent SQL:

```sql
UPDATE emp_details
SET ...
WHERE ...
```

---

# flush()

Purpose:

```text
Synchronize Persistence Context
with Database.
```

---

# Internal Flow

```text
Persistence Context
       ↓
flush()
       ↓
Pending SQL Generated
       ↓
Database Updated
```

---

# 28. Persistence Context

Persistence Context is:

```text
First Level Cache of Hibernate.
```

---

# Stores

```text
Managed Entities
```

during transaction.

---

# Example

```java
Employee e =
entityManager.find(...);

Employee e2 =
entityManager.find(...);
```

---

# Internal Behavior

```text
First Query
      ↓
Database Hit
      ↓
Stored in Persistence Context

Second Query
      ↓
Returned from Cache
```

No SQL executed second time.

---

# Visual

```text
Database
    ↓
Persistence Context
    ↓
Application
```

---

# 29. Dirty Checking

One of Hibernate's powerful features.

---

# Meaning

Hibernate automatically detects changes.

---

Example

```java
Employee emp =
entityManager.find(...);

emp.setCity("Delhi");
```

No save called.

---

# Internally

```text
Entity Loaded
      ↓
Snapshot Created
      ↓
Entity Modified
      ↓
Transaction Commit
      ↓
Snapshot Compared
      ↓
UPDATE Generated
```

---

# SQL

```sql
UPDATE emp_details
SET city='Delhi'
WHERE emp_id=?;
```

---

# 30. Transaction Commit Flow

Complete Database Flow:

```text
Begin Transaction
        ↓
Entity Operations
        ↓
Persistence Context Updated
        ↓
Dirty Checking
        ↓
flush()
        ↓
SQL Generated
        ↓
Commit Transaction
        ↓
Database Updated
```

---

# 31. Full JPA Internal Architecture

```text
Application
     ↓
Spring Boot
     ↓
IOC Container
     ↓
Repository
     ↓
JpaRepository Proxy
     ↓
EntityManager
     ↓
Hibernate Session
     ↓
Persistence Context
     ↓
JDBC
     ↓
Database
```

---

# Final Mental Picture

```text
You:
repo.save(employee)

Spring:
I'll delegate to JPA.

JPA:
I'll delegate to Hibernate.

Hibernate:
I'll generate SQL.

JDBC:
I'll send SQL.

Database:
Data Saved.
```

---

## End of Part 4

Covered Topics:

✔ Spring Boot Main Class

✔ SpringApplication.run()

✔ ConfigurableApplicationContext

✔ getBean()

✔ Operations Flow

✔ save() Internal Flow

✔ Hibernate Responsibilities

✔ EntityManager

✔ persist()

✔ merge()

✔ remove()

✔ find()

✔ flush()

✔ Persistence Context

✔ Dirty Checking

✔ Transaction Commit Flow

✔ Complete JPA Architecture
