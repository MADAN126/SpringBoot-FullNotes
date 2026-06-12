# Part 7 → JPA_MASTER_NOTES.md (Operations Class Deep Dive & Complete Save Execution Flow)

---

# 49. Operations Class

In your Main Class:

```java
ConfigurableApplicationContext container =
        SpringApplication.run(
                EmpDetailsApplication.class,
                args);

Operations op1 =
        container.getBean(
                "operations",
                Operations.class);

op1.AddUserWithMultipleParking();
```

---

# Purpose of Operations Class

The Operations class acts as:

```text
Business Layer

OR

Testing Layer
```

used to perform JPA operations.

Example:

```text
Insert

Update

Delete

Fetch

Relationship Testing
```

---

# Internal Flow

```text
Main Method
     ↓
IOC Container Starts
     ↓
Operations Bean Retrieved
     ↓
Business Method Called
     ↓
Repository Methods Executed
     ↓
Hibernate Generates SQL
     ↓
Database Updated
```

---

# 50. AddUserWithMultipleParking()

From your Main Class:

```java
op1.AddUserWithMultipleParking();
```

This method most likely performs:

```text
Create Department

Create Address

Create Employee

Create Multiple Parking Objects

Link Relationships

Save Employee
```

---

# Overall Relationship

```text
Department
     ↑
     |
Employee
     |
     ↓
Address

Employee
     |
     ↓
Parking
Parking
Parking
```

---

# Visual Representation

```text
Employee
(emp_details)
      |
      | OneToOne
      ↓
AddrDetails
(Addr)

      |
      | OneToMany
      ↓
Parking
Parking
Parking

      |
      | ManyToOne
      ↓
Department
```

---

# 51. Step 1 : Create Department

Example:

```java
Department dept =
new Department();

dept.setDeptId(1);

dept.setDeptName("IT");
```

---

## Object State

```text
Transient State

Not managed.

Not saved.
```

---

# Memory

```text
Department

deptId = 1

deptName = IT
```

---

# 52. Step 2 : Create Address

Example

```java
AddrDetails addr =
new AddrDetails();

addr.setLocation("Madhapur");

addr.setCity("Hyderabad");

addr.setCountry("India");
```

---

# Object State

```text
Transient
```

---

# Memory

```text
AddrDetails

location = Madhapur

city = Hyderabad

country = India
```

---

# 53. Step 3 : Create Employee

Example

```java
EmployeeDetails emp =
new EmployeeDetails();

emp.setEmployeeId(101);

emp.setName("Madhan");

emp.setAge(24);

emp.setSalary(65000);

emp.setCity("Hyderabad");

emp.setGender("Male");

emp.setCountry("India");
```

---

# Employee State

```text
Transient
```

---

# Memory

```text
EmployeeDetails

id = 101

name = Madhan

salary = 65000
```

---

# 54. Step 4 : Link Address

Example

```java
emp.setAddr(addr);
```

---

# Relationship

```text
Employee
    ↓
Address
```

---

# Internally

```text
Employee.addr

contains

Address Object Reference
```

---

# Memory

```text
Employee
     ↓
AddrDetails
```

---

# 55. Step 5 : Create Parking Objects

Example

```java
Parking p1 =
new Parking(true,1,2000);

Parking p2 =
new Parking(false,2,1500);

Parking p3 =
new Parking(true,3,3000);
```

---

# Meaning

```text
Parking 1

Car

Floor 1

Subscription 2000
```

---

```text
Parking 2

Bike

Floor 2

Subscription 1500
```

---

```text
Parking 3

Car

Floor 3

Subscription 3000
```

---

# Memory

```text
P1

P2

P3
```

---

# 56. Link Parking to Employee

Example

```java
List<Parking> list =
Arrays.asList(
        p1,
        p2,
        p3);

emp.setParking(list);
```

---

# Relationship

```text
Employee
     ↓
P1

P2

P3
```

---

# Memory Diagram

```text
Employee
   |
   +---- Parking 1

   |
   +---- Parking 2

   |
   +---- Parking 3
```

---

# 57. Link Department

Example

```java
emp.setDepartment(dept);
```

---

# Relationship

```text
Department
      ↑
      |
Employee
```

---

# Meaning

```text
Many Employees

can belong to

One Department
```

---

# Memory Diagram

```text
Department

      ↑

Employee
```

---

# 58. Save Employee

Example

```java
repo.save(emp);
```

---

# This is the MOST IMPORTANT LINE

Because:

```text
Entire Object Graph
gets persisted.
```

---

# Internal Flow

```text
save(Employee)
      ↓
JpaRepository Proxy
      ↓
EntityManager.persist()
      ↓
Hibernate Session
      ↓
Cascade Handling
      ↓
SQL Generation
      ↓
Database Insert
```

---

# 59. Cascade Analysis

Employee:

```java
@OneToOne(
cascade=CascadeType.ALL)

private AddrDetails addr;
```

---

Meaning

```text
Save Employee
      ↓
Save Address
```

Automatically.

---

Employee:

```java
@OneToMany(
cascade=CascadeType.ALL)

private List<Parking> parking;
```

---

Meaning

```text
Save Employee
      ↓
Save Parking List
```

Automatically.

---

Department:

```java
@ManyToOne

private Department department;
```

---

No Cascade Defined.

Meaning:

```text
Department should already exist.

OR

Must be saved separately.
```

---

# 60. SQL Generated

Step 1

Address Saved

```sql
INSERT INTO Addr
(id,location,city,country)
VALUES(...);
```

---

Step 2

Employee Saved

```sql
INSERT INTO emp_details
(emp_id,
name,
age,
salary,
city,
gender,
country,
addr_id,
dept_emp_id)

VALUES(...);
```

---

Step 3

Parking Saved

```sql
INSERT INTO Parking
(id,
emp_id,
iscar,
floorno,
monthlySub)

VALUES(...);
```

---

Parking 2

```sql
INSERT INTO Parking(...)
VALUES(...);
```

---

Parking 3

```sql
INSERT INTO Parking(...)
VALUES(...);
```

---

# SQL Order

```text
Address
    ↓
Employee
    ↓
Parking
Parking
Parking
```

---

# Database Tables After Save

---

## Addr

| id | location | city | country |
|-----|-----------|-------|----------|
| 1 | Madhapur | Hyderabad | India |

---

## Department

| deptId | deptName |
|---------|-----------|
| 1 | IT |

---

## emp_details

| emp_id | name | addr_id | dept_emp_id |
|---------|-------|----------|--------------|
| 101 | Madhan | 1 | 1 |

---

## Parking

| id | emp_id | floor |
|----|---------|--------|
|100|101|1|
|110|101|2|
|120|101|3|

---

# 61. Complete Execution Flow

```text
main()
     ↓
SpringApplication.run()
     ↓
IOC Container Created
     ↓
Operations Bean Retrieved
     ↓
AddUserWithMultipleParking()
     ↓
Create Department
     ↓
Create Address
     ↓
Create Employee
     ↓
Create Parking Objects
     ↓
Set Relationships
     ↓
repo.save(employee)
     ↓
JpaRepository
     ↓
EntityManager
     ↓
Hibernate
     ↓
SQL Generated
     ↓
Database Updated
```

---

# Final Mental Picture

```text
You:
Save Employee

Spring:
I will call Repository.

Repository:
I will use EntityManager.

EntityManager:
Hibernate, persist this.

Hibernate:
I see Address and Parking.

Cascade ALL detected.

I will save everything.

Database:
All records inserted successfully.
```

---

## End of Part 7

Covered Topics:

✔ Operations Class Purpose

✔ AddUserWithMultipleParking()

✔ Object Creation Flow

✔ Relationship Linking

✔ Address Linking

✔ Parking Linking

✔ Department Linking

✔ Cascade Execution

✔ SQL Generation Order

✔ Final Database State

✔ Complete Save Internal Flow
