# 08. Injecting Primitive, String and Collection Properties

## Concept

Setter Injection is used to inject values into bean properties through setter methods.

Spring uses:

```xml
<property>
```

to inject values into bean variables.

Spring can inject:

- Primitive Data Types
- String Data Types
- Collection Data Types
  - List
  - Set
  - Map

---

# Why Do We Need It?

Without Spring:

```java
Student s = new Student();

s.setStuName("Dilip");
s.setStudId(101);
s.setAvgOfMarks(99.88);
s.setPassedOutYear((short)2022);
s.setSelected(true);
```

Programmer manually injects values.

With Spring:

```xml
<property name="stuName" value="Dilip"/>
```

Spring automatically injects values.

---

# Setter Injection Syntax

```xml
<property
        name="variableName"
        value="actualValue"/>
```

---

# property Tag Attributes

## name

Represents bean variable name.

Example:

```xml
<property name="stuName"/>
```

Spring searches for:

```java
private String stuName;
```

and

```java
setStuName()
```

---

## value

Actual value to inject.

Example:

```xml
<property
        name="stuName"
        value="Dilip"/>
```

---

# Primitive and String Injection

## Student Bean

```java
public class Student {

    private String stuName;
    private int studId;
    private double avgOfMarks;
    private short passedOutYear;
    private boolean isSelected;

}
```

---

## XML Configuration

```xml
<bean id="studentOne"
      class="com.naresh.hello.Student">

    <property
            name="stuName"
            value="Dilip"/>

    <property
            name="studId"
            value="101"/>

    <property
            name="avgOfMarks"
            value="99.88"/>

    <property
            name="passedOutYear"
            value="2022"/>

    <property
            name="isSelected"
            value="true"/>

</bean>
```

---

# Internal Spring Execution

Spring internally performs:

```java
Student studentOne =
        new Student();

studentOne.setStuName(
        "Dilip");

studentOne.setStudId(
        101);

studentOne.setAvgOfMarks(
        99.88);

studentOne.setPassedOutYear(
        (short)2022);

studentOne.setSelected(
        true);
```

---

# Type Conversion

Spring automatically converts:

| XML Value | Java Type |
|------------|------------|
| "101" | int |
| "99.88" | double |
| "2022" | short |
| "true" | boolean |

---

# Output

```text
101
Dilip
2022
99.88
true
```

---

# Collection Injection

Spring supports collection properties.

| Collection | Tag |
|------------|------------|
| List | <list> |
| Set | <set> |
| Map | <map> |

---

# List Injection

## Property

```java
private List<String> emails;
```

---

## XML

```xml
<property name="emails">

    <list>

        <value>dilip@gmail.com</value>

        <value>laxmi@gmail.com</value>

        <value>dilip@gmail.com</value>

    </list>

</property>
```

---

## Output

```text
[dilip@gmail.com,
 laxmi@gmail.com,
 dilip@gmail.com]
```

---

## Important

```text
List allows duplicates.
```

---

## Internal Execution

```java
List<String> emails =
        new ArrayList<>();

emails.add(
        "dilip@gmail.com");

emails.add(
        "laxmi@gmail.com");

emails.add(
        "dilip@gmail.com");
```

---

# Set Injection

## Property

```java
private Set<String> mobileNumbers;
```

---

## XML

```xml
<property name="mobileNumbers">

    <set>

        <value>8826111377</value>

        <value>8826111377</value>

        <value>+1234567890</value>

    </set>

</property>
```

---

## Output

```text
[8826111377,
 +1234567890]
```

---

## Important

```text
Set removes duplicates.
```

---

## Internal Execution

```java
Set<String> mobiles =
        new HashSet<>();

mobiles.add(
        "8826111377");

mobiles.add(
        "8826111377");

mobiles.add(
        "+1234567890");
```

Final Result:

```text
[8826111377,
 +1234567890]
```

---

# Map Injection

## Property

```java
private Map<String,String> subMarks;
```

---

## XML

```xml
<property name="subMarks">

    <map>

        <entry
                key="maths"
                value="88"/>

        <entry
                key="science"
                value="66"/>

        <entry
                key="english"
                value="44"/>

    </map>

</property>
```

---

## Output

```text
{
 maths=88,
 science=66,
 english=44
}
```

---

## Important

```text
Map stores data as
Key → Value
pairs.
```

---

## Internal Execution

```java
Map<String,String> marks =
        new HashMap<>();

marks.put(
        "maths",
        "88");

marks.put(
        "science",
        "66");

marks.put(
        "english",
        "44");
```

---

# Complete Flow

```text
beans.xml
    │
    ▼
Spring IOC Container Starts
    │
    ▼
Student Object Created
    │
    ▼
Setter Methods Called
    │
    ▼
Primitive Values Injected
    │
    ▼
Collection Values Injected
    │
    ▼
Bean Stored In Container
    │
    ▼
getBean()
    │
    ▼
Object Returned
```

---

# Real Project Usage

## List

```text
Email Addresses
Course Names
Skills
```

---

## Set

```text
Phone Numbers
User Roles
Unique IDs
```

---

## Map

```text
Subject → Marks

Product → Price

Country → Capital
```

---

# Common Mistakes

## Wrong Property Name

Bean:

```java
private String stuName;
```

XML:

```xml
<property
        name="studentName"
        value="Dilip"/>
```

Result:

```text
BeanCreationException
```

---

## Setter Missing

Bean Variable Exists

```java
private String stuName;
```

Setter Missing

```java
setStuName()
```

Result:

```text
Spring cannot inject value.
```

---

## Wrong Data Type

```xml
<property
        name="studId"
        value="ABC"/>
```

Expected:

```java
int
```

Result:

```text
TypeMismatchException
```

---

# Interview Questions

## Which tag is used for Setter Injection?

```xml
<property>
```

---

## Which tag injects List?

```xml
<list>
```

---

## Which tag injects Set?

```xml
<set>
```

---

## Which tag injects Map?

```xml
<map>
```

---

## Which collection allows duplicates?

```text
List
```

---

## Which collection removes duplicates?

```text
Set
```

---

## How does Spring inject values?

```text
Using Setter Methods
```

---

# Quick Revision

| Type | XML Tag |
|--------|--------|
| Primitive | <property> |
| String | <property> |
| List | <list> |
| Set | <set> |
| Map | <map> |

---

# Memory Trick

```text
property
      ↓
Setter Method
      ↓
Value Injected
```

```text
List
=
Duplicates Allowed
```

```text
Set
=
Duplicates Removed
```

```text
Map
=
Key → Value
```

---

# One-Line Exam Answer

```text
Spring Setter Injection uses the <property> tag to inject primitive, String, List, Set and Map values into bean properties through setter methods.
```
