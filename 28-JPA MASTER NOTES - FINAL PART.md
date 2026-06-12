FINAL PART 

---

# PART 8 – Operations Class & Practical JPA Execution Flow

---

## Application Entry Point

```java
@SpringBootApplication
public class EmpDetailsApplication {

    public static void main(String[] args) {

        ConfigurableApplicationContext container =
                SpringApplication.run(EmpDetailsApplication.class, args);

        Operations op1 =
                (Operations) container.getBean("operations");

        op1.AddUserWithMultipleParking();
    }
}
```

---

# What happens internally?

```text
main()
 ↓
SpringApplication.run()
 ↓
Create ApplicationContext
 ↓
Auto Configuration
 ↓
Component Scan
 ↓
Repository Proxies Created
 ↓
Hibernate Started
 ↓
Datasource Initialized
 ↓
EntityManagerFactory Created
 ↓
Beans Created
 ↓
ApplicationContext Returned
 ↓
getBean("operations")
 ↓
AddUserWithMultipleParking()
```

---

# SpringApplication.run()

Starts the entire Spring Boot application.

It performs:

1. Creates IoC Container
2. Starts Embedded Server (if web application)
3. Performs Component Scan
4. Creates Beans
5. Applies Auto Configuration
6. Initializes JPA
7. Returns ApplicationContext

---

# ConfigurableApplicationContext

```java
ConfigurableApplicationContext container;
```

Extension of ApplicationContext.

Used for:

```java
getBean()

close()

refresh()

registerShutdownHook()
```

---

# getBean()

```java
Operations op1 =
container.getBean("operations");
```

Internally:

```text
Search Bean Definition
 ↓
Find Operations Bean
 ↓
Return Existing Singleton Object
```

---

# Operations Class

Purpose:

Acts as Service Layer.

Responsible for:

```text
Calling Repositories

Building Objects

Saving Entities

Fetching Data

Deleting Data
```

---

# Example

```java
@Component
public class Operations {

    @Autowired
    Repo repo;

    public void AddUserWithMultipleParking(){

    }
}
```

---

# Dependency Injection

```text
Operations
    ↓
needs Repo
    ↓
Spring injects Repo Proxy
```

---

# Flow

```text
Operations Bean
 ↓
@Autowired Repo
 ↓
Spring Data Proxy
 ↓
Hibernate
 ↓
Database
```

---

# Example Insert

```java
AddrDetails addr =
new AddrDetails("MG Road","Mumbai","India");

Parking p1 =
new Parking(true,2,2000);

Parking p2 =
new Parking(false,1,1000);

EmployeeDetails emp =
new EmployeeDetails(
101,
"John",
25,
50000,
"Mumbai",
"Male",
"India"
);

emp.setAddr(addr);

emp.setParking(List.of(p1,p2));

repo.save(emp);
```

---

# Internal Save Flow

```text
repo.save(emp)
 ↓
Hibernate checks entity state
 ↓
Employee NEW
 ↓
Persist Employee
 ↓
Cascade to Address
 ↓
Cascade to Parking
 ↓
SQL Generated
 ↓
Commit Transaction
```

---

# SQL Order

Employee owns:

```text
Address

Parking
```

Therefore:

```sql
INSERT ADDRESS

INSERT EMPLOYEE

INSERT PARKING

INSERT PARKING
```

---

# Why?

Because Employee contains FK references.

Dependencies must exist first.

---

# Entity Graph

```text
Employee
 ├── Address
 └── Parking
      ├── Parking 1
      └── Parking 2
```

---

# Persistence Context

Hibernate keeps entities in memory.

```text
save()
 ↓
Persistence Context
 ↓
Flush
 ↓
Database
```

---

# Flush

Flush means:

```text
Convert Entity Changes
into SQL
```

NOT COMMIT.

---

# Commit

Commit means:

```text
Permanently save changes
```

---

# PART 9 – Entity States & Persistence Context

---

# Entity Lifecycle States

JPA Entities have four states.

---

## 1. Transient

Object created.

Not managed.

Not saved.

Example:

```java
EmployeeDetails emp =
new EmployeeDetails();
```

Flow:

```text
new Employee()
 ↓
Transient
```

---

## 2. Persistent

Managed by Hibernate.

Example:

```java
repo.save(emp);
```

Flow:

```text
Transient
 ↓
save()
 ↓
Persistent
```

---

## 3. Detached

Previously managed.

No longer tracked.

Example:

```java
context.close();
```

Flow:

```text
Persistent
 ↓
Context Closed
 ↓
Detached
```

---

## 4. Removed

Scheduled for deletion.

Example:

```java
repo.delete(emp);
```

Flow:

```text
Persistent
 ↓
delete()
 ↓
Removed
```

---

# Lifecycle Diagram

```text
Transient
   ↓ save()
Persistent
   ↓ close()
Detached
   ↓ delete()
Removed
```

---

# Persistence Context

Persistence Context is:

> First-level cache managed by EntityManager.

Stores managed entities.

---

# Internal Flow

```text
Database
 ↓
Entity Loaded
 ↓
Persistence Context
 ↓
Application Uses Entity
 ↓
Hibernate Tracks Changes
 ↓
Flush
 ↓
Database Updated
```

---

# Dirty Checking

Hibernate automatically detects modifications.

Example:

```java
emp.setSalary(80000);
```

Without save:

```text
Managed Entity Changed
 ↓
Dirty Checking
 ↓
UPDATE Generated
```

---

# SQL

```sql
UPDATE emp_details
SET salary=80000
WHERE emp_id=101;
```

---

# PART 10 – Transactions

---

# What is a Transaction?

Transaction means:

> Group of operations executed as one unit.

---

# Example

Transfer money:

```text
Debit Account A
Credit Account B
```

Both should happen.

Otherwise rollback.

---

# ACID Properties

---

## Atomicity

All or nothing.

---

## Consistency

Valid state maintained.

---

## Isolation

Transactions independent.

---

## Durability

Committed changes survive failures.

---

# @Transactional

Used to manage transactions.

Example:

```java
@Transactional
public void saveEmployee(){

    repo.save(emp);

}
```

---

# Internal Flow

```text
Method Starts
 ↓
Transaction Opens
 ↓
Execute SQL
 ↓
Success?
 ├── Yes → Commit
 └── No  → Rollback
```

---

# Commit Flow

```text
Begin Transaction
 ↓
Execute Queries
 ↓
Commit
```

---

# Rollback Flow

```text
Begin Transaction
 ↓
Execute Queries
 ↓
Exception Occurred
 ↓
Rollback
```

---

# Why Transactions?

Without transaction:

```text
INSERT Employee
Success

INSERT Parking
Fails

Database becomes inconsistent.
```

With transaction:

```text
INSERT Employee
INSERT Parking
Fails
 ↓
Rollback
 ↓
Nothing Saved
```

---

# Repository Execution Flow

```text
Application
 ↓
Operations
 ↓
Repo
 ↓
Spring Data Proxy
 ↓
EntityManager
 ↓
Hibernate
 ↓
Persistence Context
 ↓
SQL
 ↓
Database
```

---

# Complete JPA Mental Model

```text
Spring Boot Starts
 ↓
ApplicationContext Created
 ↓
Repositories Generated
 ↓
Hibernate Initialized
 ↓
EntityManagerFactory Created
 ↓
Operations Bean Retrieved
 ↓
Business Method Called
 ↓
Entities Created
 ↓
Repository save()
 ↓
Persistence Context
 ↓
Dirty Checking
 ↓
Flush
 ↓
SQL Generated
 ↓
Transaction Commit
 ↓
Database Updated
```

---

# Final JPA Picture

```text
YOU
 ↓
Operations Class
 ↓
Repository
 ↓
Spring Data JPA
 ↓
Hibernate
 ↓
Persistence Context
 ↓
Entity States
 ↓
Transactions
 ↓
Database
```

---

# JPA MASTER NOTES COMPLETED

Covered Topics:

✓ JPA Introduction

✓ Spring Data JPA Architecture

✓ Entity Mapping

✓ Primary Keys

✓ Sequence Generators

✓ One-To-One Mapping

✓ One-To-Many Mapping

✓ Many-To-One Mapping

✓ Department Mapping

✓ Repository CRUD Operations

✓ Derived Query Methods

✓ JPQL Queries

✓ Native Queries

✓ Named Parameters

✓ Positional Parameters

✓ Operations Class Execution Flow

✓ ApplicationContext

✓ SpringApplication.run()

✓ Persistence Context

✓ Entity Lifecycle States

✓ Dirty Checking

✓ Flush vs Commit

✓ Transactions

✓ @Transactional

✓ Complete Internal JPA Execution Flow
