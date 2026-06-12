# Part 3 → JPA_MASTER_NOTES.md (Advanced JPA Relationships & Custom Queries)

---

# 14. Repository Layer (Spring Data JPA)

Spring Data JPA reduces boilerplate code by automatically generating CRUD operations.

Instead of writing DAO implementations manually:

```java
public class EmployeeDao {
    public void save(Employee e){
        // JDBC code
    }
}
```

we simply extend:

```java
JpaRepository<Entity, PrimaryKeyType>
```

Example:

```java
public interface Repo extends JpaRepository<EmployeeDetails, Long> {

}
```

---

## Internal Flow

```text
Application Starts
        ↓
Spring Boot scans repositories
        ↓
JpaRepository implementation generated dynamically
        ↓
Bean registered in IOC Container
        ↓
Injected wherever required
```

---

## Syntax

```java
public interface Repo extends JpaRepository<EmployeeDetails, Long> {

}
```

Where:

```text
EmployeeDetails → Entity Class

Long → Primary Key Type
```

---

# Advantages of JpaRepository

Provides:

```text
save()

saveAll()

findById()

findAll()

existsById()

delete()

deleteById()

count()

flush()

findAll(Pageable)
```

without implementation.

---

# 15. Custom JPQL Query

JPQL = Java Persistence Query Language.

JPQL works on:

```text
Entity Names

NOT Database Table Names
```

---

Example:

```java
@Query(value="SELECT E FROM EmployeeDetails E",
        nativeQuery=false)
List<EmployeeDetails> getAllRecords();
```

---

## Understanding

Entity:

```java
@Entity
@Table(name="emp_details")
public class EmployeeDetails {

}
```

JPQL:

```java
SELECT E FROM EmployeeDetails E
```

NOT:

```sql
SELECT * FROM emp_details
```

because JPQL uses entity names.

---

## Internal Flow

```text
Method Called
        ↓
Spring reads @Query
        ↓
JPQL detected
        ↓
Converted to SQL
        ↓
SQL executed
        ↓
Entities returned
```

---

# 16. Native Queries

Native Query means:

```text
Direct SQL
```

Database table names are used.

---

Example:

```java
@Query(value =
"SELECT * FROM emp_details
 WHERE COUNTRY=:COUNTRY
 AND AGE<:AGE",

nativeQuery=true)

List<EmployeeDetails>
getEmpByCountryAndAge(
@Param("COUNTRY") String country,
@Param("AGE") int age);
```

---

## Internal Flow

```text
Method Invoked
       ↓
Spring sees nativeQuery=true
       ↓
No JPQL conversion
       ↓
SQL sent directly
       ↓
Database executes
       ↓
Rows converted into Entities
```

---

# Named Parameters

Example:

```java
@Param("COUNTRY")
```

Used as:

```sql
:COUNTRY
```

---

Advantage:

Instead of:

```sql
?1
?2
```

we use:

```sql
:COUNTRY
:AGE
```

making queries readable.

---

# Example

```java
repo.getEmpByCountryAndAge(
       "India",
       30
);
```

SQL:

```sql
SELECT *
FROM emp_details
WHERE COUNTRY='India'
AND AGE<30;
```

---

# 17. Positional Parameters

Spring also supports positional placeholders.

Example:

```java
@Query(value=
"SELECT *
 FROM emp_details
 WHERE GENDER=?1
 AND AGE=?2",

nativeQuery=true)

List<EmployeeDetails>
getEmpByGenderAndAge(
String gender,
int age);
```

---

## Meaning

```text
?1 → first parameter

?2 → second parameter
```

---

Example

```java
repo.getEmpByGenderAndAge(
       "Male",
       25
);
```

becomes

```sql
SELECT *
FROM emp_details
WHERE GENDER='Male'
AND AGE=25;
```

---

# 18. Derived Query Methods

Spring generates SQL from method names.

No SQL writing required.

---

Example

```java
List<EmployeeDetails>
findByCity(String city);
```

Generated SQL:

```sql
SELECT *
FROM emp_details
WHERE city=?;
```

---

Example

```java
repo.findByCity("Mumbai");
```

returns

```text
All Employees from Mumbai
```

---

# findByGender()

Method:

```java
List<EmployeeDetails>
findByGender(String gender);
```

Generated SQL:

```sql
SELECT *
FROM emp_details
WHERE gender=?;
```

---

# findBySalary()

Method:

```java
List<EmployeeDetails>
findBySalary(int salary);
```

Generated SQL:

```sql
SELECT *
FROM emp_details
WHERE salary=?;
```

---

# Multiple Conditions

Method:

```java
List<EmployeeDetails>
findByGenderAndCountry(
String gender,
String country);
```

Generated SQL:

```sql
SELECT *
FROM emp_details
WHERE gender=?
AND country=?;
```

---

Example

```java
repo.findByGenderAndCountry(
       "Female",
       "India");
```

---

# Internal Flow of Derived Queries

```text
Repository Method Called
        ↓
Spring analyses method name
        ↓
Creates JPQL internally
        ↓
Converts to SQL
        ↓
Executes Query
        ↓
Returns Entities
```

---

# 19. Delete Derived Queries

Spring can generate delete SQL.

---

Example

```java
int deleteByCity(String city);
```

Generated SQL:

```sql
DELETE
FROM emp_details
WHERE city=?;
```

---

Example

```java
repo.deleteByCity("Delhi");
```

---

Return Value

```text
Number of Rows Deleted
```

Example:

```java
2
```

means

```text
2 employee records removed.
```

---

# deleteByName()

Method:

```java
int deleteByName(String name);
```

Generated SQL:

```sql
DELETE
FROM emp_details
WHERE name=?;
```

---

# Internal Flow

```text
deleteByCity()
       ↓
Spring derives DELETE query
       ↓
Database executes DELETE
       ↓
Affected row count returned
```

---

# 20. Department Repository

Repository:

```java
public interface DeptRepo
extends JpaRepository<
Department,
Integer>{

}
```

---

Meaning

```text
Department → Entity

Integer → Primary Key
```

---

Provides:

```text
save()

findAll()

delete()

findById()

count()
```

automatically.

---

# 21. Parking Repository

Repository:

```java
public interface ParkingRepo
extends JpaRepository<
Parking,
Integer>{

}
```

---

Provides CRUD for Parking.

---

Example

Save Parking:

```java
Parking p=new Parking();

parkingRepo.save(p);
```

---

# Internal Architecture of Spring Data JPA

```text
Controller
      ↓
Service
      ↓
Repository Interface
      ↓
JpaRepository Proxy
      ↓
EntityManager
      ↓
Hibernate
      ↓
JDBC
      ↓
Database
```

---

# Final Mental Picture

```text
Entity
   ↓
Repository
   ↓
JpaRepository
   ↓
Spring Data JPA
   ↓
Hibernate
   ↓
SQL
   ↓
Database
```

---

## End of Part 3

Covered Topics:

✔ JpaRepository

✔ JPQL Queries

✔ Native Queries

✔ Named Parameters

✔ Positional Parameters

✔ Derived Queries

✔ Delete Queries

✔ DeptRepo

✔ ParkingRepo

✔ Spring Data Internal Flow
