# Spring MVC & Spring Data JPA — Query Parameters, @RequestParam, Custom Queries (JPQL, Native SQL, Named Queries)

---

# The Bigger Picture

Until now, our APIs mostly looked like this:

```text
GET /users/123
```

or

```text
POST /users
```

But real applications often need something different.

Examples:

```text
Find users by email
Find users by city
Filter products by category
Search employees by department
```

These aren't "specific resources."

They're filters.

This chapter explains how Spring handles filtering requests and how Spring Data JPA retrieves that filtered data.

---

# Query Parameters

---

# Situation

Suppose Flipkart has millions of users.

You need:

> Find users whose email or mobile number matches.

Input:

```text
email
mobile number
```

---

# Problem

Passing these values as JSON feels unnecessary.

Example:

```json
{
    "email":"abc@gmail.com",
    "mobile":"9999999999"
}
```

for a GET request.

Not ideal.

---

# Solution

Use Query Parameters.

---

# Mental Model

Think of query parameters as:

> Filters attached to a URL.

---

# URI Structure

```text
Base URL
   +
?
   +
parameter=value
```

---

# Example

```text
/details?email=dilip@gmail.com&mobileNumber=888888
```

---

# Breakdown

| Part | Meaning |
|----------|------------|
| /details | Resource |
| ? | Query string starts |
| email=dilip@gmail.com | Filter 1 |
| & | Separator |
| mobileNumber=888888 | Filter 2 |

---

# Visual Flow

```text
URL
 │
 ▼
/details
 │
 ▼
email=dilip@gmail.com
 │
 ▼
mobileNumber=888888
 │
 ▼
Controller Receives Filters
```

---

# Controller

---

## Situation

Need filters from URL.

---

## Solution

Use:

# @RequestParam

---

# Controller Code

```java
@GetMapping("/details")
public List<Users> getUsersByEmailOrMobile(

        @RequestParam String email,

        @RequestParam String mobileNumber) {

    return service.getUsersByEmailOrMobile(
            email,
            mobileNumber);
}
```

---

# Internal Flow

```text
HTTP Request
      │
      ▼
Spring MVC
      │
Reads Query String
      │
email=
mobileNumber=
      │
Binds Values
      │
Method Parameters
      │
Controller Executes
```

---

# Example

Request:

```text
/details?email=test@gmail.com&mobileNumber=99999
```

Spring executes:

```java
email = "test@gmail.com"

mobileNumber = "99999"
```

---

# Service Layer

```java
public List<Users> getUsersByEmailOrMobile(

        String email,
        String mobileNumber) {

    return repository
            .findByEmailOrMobileNumber(
                    email,
                    mobileNumber);
}
```

---

# Derived Query Method

```java
List<Users>
findByEmailOrMobileNumber(

        String email,
        String mobileNumber);
```

---

# What SQL Does Spring Generate?

---

# Method Name

```java
findByEmailOrMobileNumber(...)
```

---

# Spring Understands

```text
Email
OR
Mobile Number
```

---

# Generated SQL

Equivalent SQL:

```sql
SELECT *
FROM flipkart_users
WHERE email=?
OR mobile_number=?;
```

---

# Internal Flow

```text
Repository Method
       │
Spring Parses Method Name
       │
Email OR Mobile
       │
Hibernate Generates SQL
       │
Database Executes
       │
Entities Returned
```

---

# Entity Class

---

# Database Table

```sql
CREATE TABLE flipkart_users(

    first_name VARCHAR2(30),
    last_name VARCHAR2(30),

    mobile_number VARCHAR2(15),

    email VARCHAR2(50) PRIMARY KEY,

    password VARCHAR2(30),

    city VARCHAR2(30),

    pincode NUMBER(6)
);
```

---

# Entity Mapping

```java
@Entity
@Table(name="flipkart_users")
public class Users {

    @Column(name="first_name")
    private String firstName;

    @Column(name="last_name")
    private String lastName;

    @Column(name="mobile_number")
    private String mobileNumber;

    @Id
    private String email;

    private String password;

    private String city;

    private int pincode;
}
```

---

# Why @Column Sometimes Exists

---

## Situation

Database column:

```sql
first_name
```

Java field:

```java
firstName
```

Different names.

---

## Solution

Use:

```java
@Column(name="first_name")
```

---

# Situation

Database:

```sql
password
```

Java:

```java
password
```

Same name.

---

## Result

No annotation needed.

---

# Internal Mapping

```text
Java Field
     │
@Column
     │
Database Column
```

---

# Required vs Optional Query Parameters

---

# Situation

Suppose mobile number is optional.

Users may search using:

```text
Email only
```

---

# Problem

By default Spring requires all parameters.

Missing parameter causes failure.

---

# Example

Request:

```text
/details?email=test@gmail.com
```

Controller:

```java
@RequestParam String mobileNumber
```

Result:

```text
400 Bad Request
```

---

# Why?

Spring thinks:

> "You said this parameter is mandatory."

---

# Solution

Make it optional.

---

# Code

```java
@GetMapping("/details")
public List<Users> getUsersByEmailOrMobile(

        @RequestParam String email,

        @RequestParam(required=false)
        String mobileNumber) {

    return service.getUsersByEmailOrMobile(
            email,
            mobileNumber);
}
```

---

# Internal Flow

```text
Request Arrives
      │
mobileNumber Present?
      │
 ┌────┴────┐
 │         │
Yes        No
 │         │
Value      null
 │         │
Controller Executes
```

---

# Result

These both work:

```text
/details?email=test@gmail.com
```

and

```text
/details?email=test@gmail.com&mobileNumber=99999
```

---

# Multi-Value Query Parameters

---

# Situation

Need multiple IDs.

Example:

```text
1
2
3
```

---

# Solution

Use List.

---

# Controller

```java
@GetMapping("/api")
public String getUsers(

        @RequestParam List<String> id) {

    return "IDs are " + id;
}
```

---

# Supported Formats

---

## Comma Separated

```text
/api?id=1,2,3
```

Spring creates:

```java
["1","2","3"]
```

---

## Repeated Parameters

```text
/api?id=1&id=2&id=3
```

Spring creates:

```java
["1","2","3"]
```

---

# Internal Flow

```text
Query Parameters
       │
Spring Detects List
       │
Split Values
       │
Create Java List
```

---

# Mapping All Query Parameters

---

# Situation

You don't know parameter names beforehand.

Example:

```text
?page=1&sort=name&city=Hyderabad
```

---

# Solution

Use Map.

---

# Controller

```java
@GetMapping("/api")
public String getUsers(

        @RequestParam
        Map<String,String> allParams) {

    return allParams.toString();
}
```

---

# Internal Flow

```text
Query String
      │
Spring Reads All Parameters
      │
Creates Map
      │
Key → Value
```

---

# Example

Input:

```text
?page=1&city=Hyderabad
```

Map:

```java
{
    "page":"1",
    "city":"Hyderabad"
}
```

---

# Query Parameter vs Path Variable

---

# Mental Model

Ask yourself:

> Am I identifying a resource?

OR

> Am I filtering resources?

---

# Use Path Variable

When identifying.

Example:

```text
/users/123
```

Meaning:

```text
Fetch user 123
```

---

# Use Query Parameters

When filtering.

Example:

```text
/users?occupation=programmer
```

Meaning:

```text
Fetch programmers
```

---

# Comparison

| Use Case | Path Variable | Query Parameter |
|----------|-----------|-------------|
| Identify one resource | ✅ | ❌ |
| Filtering | ❌ | ✅ |
| Sorting | ❌ | ✅ |
| Pagination | ❌ | ✅ |
| Required resource ID | ✅ | ❌ |

---

# Native Queries

---

# Situation

Derived Query Methods are powerful.

But sometimes:

You need:

```text
Complex SQL
Database-specific SQL
Optimized Queries
Joins
Stored Procedure-like logic
```

---

# Problem

Method names become ridiculous.

Example:

```java
findByCityAndDepartmentAndSalary...
```

---

# Solution

Write SQL yourself.

Using:

# @Query

---

# Two Types of Queries

```text
@Query
   │
   ├─ JPQL
   │
   └─ Native SQL
```

---

# JPQL

---

# Mental Model

Think of JPQL as:

> SQL written using Java entities instead of tables.

---

# Important Difference

JPQL uses:

```text
Entity Names
Java Fields
```

NOT

```text
Database Tables
Database Columns
```

---

# Example

Entity:

```java
@Entity
class Users {
    private String city;
}
```

---

# JPQL

```java
@Query(
"value=
'Select u from Users u where city=?1'")
List<Users> getUsersByCityName(
        String city);
```

---

# JPQL Translation

Spring converts:

```text
Users
```

to:

```text
flipkart_users
```

behind the scenes.

---

# Internal Flow

```text
JPQL
    │
Hibernate Parses
    │
Converts To SQL
    │
Database Executes
```

---

# Native SQL

---

# Mental Model

Think of Native SQL as:

> Direct conversation with the database.

No translation.

---

# Example

```java
@Query(

value=
"select * from flipkart_users",

nativeQuery=true)

List<Users> getUsers();
```

---

# Internal Flow

```text
Native SQL
      │
Passed Directly
      │
Database Executes
      │
Entities Created
```

---

# JPQL vs Native SQL

| Feature | JPQL | Native SQL |
|---------|------|------------|
| Uses Entities | ✅ | ❌ |
| Uses Tables | ❌ | ✅ |
| Database Independent | ✅ | ❌ |
| Vendor Specific Features | ❌ | ✅ |
| Hibernate Translation | ✅ | ❌ |

---

# Indexed Parameters

---

# Situation

Need parameters inside queries.

---

# Solution

Use positions.

---

# Example

```java
@Query(

value=
"select * from flipkart_users where city=?1",

nativeQuery=true)

List<Users> getUsersByCity(
        String city);
```

---

# How It Works

```text
?1
 │
 ▼
First Method Parameter
```

---

# Multiple Parameters

```java
@Query(

value=
"select * from flipkart_users
 where city=?1
 or pincode=?2",

nativeQuery=true)

List<Users> getUsersByCityOrPincode(

        String city,

        String pincode);
```

---

# Binding Flow

```text
Method Parameters
      │
city      pincode
 │           │
 ▼           ▼
?1          ?2
```

---

# Named Parameters

---

# Problem

Indexes become confusing.

Example:

```text
?1 ?2 ?3 ?4 ?5
```

Hard to maintain.

---

# Solution

Use names.

---

# Example

```java
@Query(

value=
"select *
 from flipkart_users
 where city=:cityName
 and pincode=:pincode",

nativeQuery=true)

List<Users> getUsersByCityAndPincode(

        @Param("cityName")
        String city,

        @Param("pincode")
        String pincode);
```

---

# Internal Flow

```text
@Param("cityName")
        │
        ▼
:cityName

@Param("pincode")
        │
        ▼
:pincode
```

---

# Why Named Parameters Are Better

```text
More Readable
Less Error Prone
Easier Refactoring
```

---

# Named Queries

---

# Situation

Suppose a query is reused many times.

Repository becomes cluttered.

---

# Problem

Large query strings everywhere.

---

# Solution

Move query definition to Entity.

Using:

# @NamedQuery

---

# Entity

```java
@Entity
@NamedQuery(

name="mobileQuery",

query=
"Select u from Users u
 where mobileNum=:mobile")

public class Users {

}
```

---

# Repository

```java
@Query(name="mobileQuery")
List<Users> getUsersByMobile(

        @Param("mobile")
        String mobileNumber);
```

---

# Internal Flow

```text
Repository Method
       │
Query Name Found
       │
mobileQuery
       │
Entity Scanned
       │
JPQL Retrieved
       │
Hibernate Executes
```

---

# Final Mental Model

```text
Client
   │
GET /users?email=x&mobile=y
   │
@RequestParam
   │
Controller
   │
Service
   │
Repository
   │
Choose Query Strategy
   │
 ┌───────────────┬─────────────┬─────────────┐
 │               │             │
 ▼               ▼             ▼
Derived       JPQL         Native SQL
Method        Query         Query
 │               │             │
Hibernate    Hibernate     Database
Generates    Converts      Executes
SQL          JPQL→SQL      Direct SQL
 │               │             │
 └───────────────┴─────────────┘
             │
             ▼
Database
   │
Entities
   │
Controller
   │
Jackson
   │
JSON Response
   │
Client
```

Spring gives multiple ways to retrieve data:

- **Derived Queries** for simple conditions.
- **JPQL** for object-oriented database queries.
- **Native SQL** when full database control is required.
- **Named Queries** when queries become reusable.

Choosing the right approach is less about memorizing annotations and more about understanding the trade-off between simplicity, readability, portability, and control.
