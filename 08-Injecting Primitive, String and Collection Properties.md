# 08. Injecting Primitive, String and Collection Properties

A Student object contains data such as:

- Name
- Id
- Average Marks
- Passed Out Year
- Selection Status

Without Spring, values must be supplied manually.

```java
Student student =
        new Student();

student.setStuName(
        "Dilip");

student.setStudId(
        101);

student.setAvgOfMarks(
        99.88);

student.setPassedOutYear(
        (short)2022);

student.setSelected(
        true);
```

As the number of objects increases, manually supplying values becomes repetitive.

Spring can perform the same work automatically.

---

Spring uses:

```xml
<property>
```

to supply values into bean properties.

```text
Spring Creates Object
         │
         ▼
Spring Calls Setter Methods
         │
         ▼
Values Injected
```

---

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

## What Spring Does

Spring creates the object:

```java
Student student =
        new Student();
```

Spring reads:

```xml
<property
        name="stuName"
        value="Dilip"/>
```

Spring calls:

```java
student.setStuName(
        "Dilip");
```

---

Spring reads:

```xml
<property
        name="studId"
        value="101"/>
```

Spring calls:

```java
student.setStudId(
        101);
```

---

Flow:

```text
XML Value
      │
      ▼
Setter Method
      │
      ▼
Bean Property
```

---

## Understanding name

```xml
<property
        name="stuName"/>
```

Spring searches for:

```java
private String stuName;
```

and

```java
setStuName();
```

The `name` attribute must match the bean property.

---

## Understanding value

```xml
<property
        name="stuName"
        value="Dilip"/>
```

The `value` attribute contains the actual data to inject.

---

## Automatic Type Conversion

XML values are always read as text.

Spring automatically converts them.

```xml
value="101"
```

becomes

```java
int
```

---

```xml
value="99.88"
```

becomes

```java
double
```

---

```xml
value="2022"
```

becomes

```java
short
```

---

```xml
value="true"
```

becomes

```java
boolean
```

---

## Output

```text
101

Dilip

2022

99.88

true
```

---

# Collection Injection

A bean may contain collections.

Example:

```java
private List<String> emails;

private Set<String> mobileNumbers;

private Map<String,String> subMarks;
```

Spring can inject these collections automatically.

---

# List Injection

Emails:

```java
private List<String> emails;
```

XML:

```xml
<property name="emails">

    <list>

        <value>dilip@gmail.com</value>

        <value>laxmi@gmail.com</value>

        <value>dilip@gmail.com</value>

    </list>

</property>
```

Result:

```text
[dilip@gmail.com,
 laxmi@gmail.com,
 dilip@gmail.com]
```

Observation:

```text
List keeps duplicates.
```

Flow:

```text
Values Added
      │
      ▼
Stored In Order
      │
      ▼
Duplicates Preserved
```

---

# Set Injection

Mobile Numbers:

```java
private Set<String> mobileNumbers;
```

XML:

```xml
<property name="mobileNumbers">

    <set>

        <value>8826111377</value>

        <value>8826111377</value>

        <value>+1234567890</value>

    </set>

</property>
```

Result:

```text
[8826111377,
 +1234567890]
```

Observation:

```text
Set removes duplicates.
```

Flow:

```text
Values Added
      │
      ▼
Duplicate Found
      │
      ▼
Duplicate Removed
```

---

# Map Injection

Subject Marks:

```java
private Map<String,String> subMarks;
```

XML:

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

Result:

```text
{
 maths=88,
 science=66,
 english=44
}
```

Observation:

```text
Map stores data as:

Key → Value
```

Example:

```text
maths   → 88

science → 66

english → 44
```

---

# Collection Comparison

## List

```text
Stores Values

Keeps Duplicates

Maintains Order
```

Example:

```text
A
A
B
```

Result:

```text
A
A
B
```

---

## Set

```text
Stores Unique Values

Removes Duplicates
```

Example:

```text
A
A
B
```

Result:

```text
A
B
```

---

## Map

```text
Stores Key → Value Pairs
```

Example:

```text
Maths → 88

Science → 66
```

---

# Complete Flow

```text
beans.xml
      │
      ▼
Spring Container Starts
      │
      ▼
Bean Object Created
      │
      ▼
Setter Methods Located
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
Ready Object Returned
```

---

# Real Usage

List:

```text
Email Addresses

Skills

Course Names
```

---

Set:

```text
Roles

Permissions

Unique Phone Numbers
```

---

Map:

```text
Subject → Marks

Product → Price

Country → Capital
```

---

# Quick Revision

```xml
<property>
```

Used for Setter Injection.

---

```xml
<list>
```

Duplicates Allowed.

---

```xml
<set>
```

Duplicates Removed.

---

```xml
<map>
```

Key → Value Storage.

---

```text
Spring Reads XML
      │
      ▼
Calls Setter
      │
      ▼
Injects Value
      │
      ▼
Bean Ready
```
