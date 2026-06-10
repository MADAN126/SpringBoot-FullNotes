# Injecting Primitive, String and Collection Properties

## Why Do We Need Property Injection?

A `Student` object contains data such as:

- Name
- Id
- Average Marks
- Passed Out Year
- Selection Status

Without Spring, every value must be supplied manually.

```java
Student student = new Student();

student.setStuName("Dilip");
student.setStudId(101);
student.setAvgOfMarks(99.88);
student.setPassedOutYear((short)2022);
student.setSelected(true);
```

As the number of objects increases, manually setting values becomes repetitive.

Spring can perform this work automatically.

---

## Solution

Spring uses the `<property>` tag to inject values into bean properties.

```text
Spring Creates Bean
         │
         ▼
Reads <property>
         │
         ▼
Calls Setter Method
         │
         ▼
Value Injected
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

## Bean Configuration

```xml
<bean id="studentOne"
      class="com.naresh.hello.Student">

    <property name="stuName"
              value="Dilip"/>

    <property name="studId"
              value="101"/>

    <property name="avgOfMarks"
              value="99.88"/>

    <property name="passedOutYear"
              value="2022"/>

    <property name="isSelected"
              value="true"/>

</bean>
```

---

## How Spring Injects Values

Spring first creates the object.

```java
Student student = new Student();
```

Then Spring reads:

```xml
<property name="stuName"
          value="Dilip"/>
```

and calls:

```java
student.setStuName("Dilip");
```

Similarly:

```xml
<property name="studId"
          value="101"/>
```

becomes:

```java
student.setStudId(101);
```

---

## Internal Flow

```text
beans.xml
      │
      ▼
Bean Created
      │
      ▼
Property Read
      │
      ▼
Matching Setter Found
      │
      ▼
Setter Executed
      │
      ▼
Value Stored
```

---

## Understanding name

```xml
<property name="stuName"/>
```

Spring looks for:

```java
private String stuName;
```

and

```java
setStuName(...)
```

The `name` attribute must match the bean property.

---

## Understanding value

```xml
<property name="stuName"
          value="Dilip"/>
```

The `value` attribute contains the actual data that Spring injects.

---

## Automatic Type Conversion

XML values are read as text.

Spring automatically converts them to the required datatype.

| XML Value | Converted To |
|------------|-------------|
| "101" | int |
| "99.88" | double |
| "2022" | short |
| "true" | boolean |

Example:

```xml
value="101"
```

becomes:

```java
int
```

without manual conversion.

---

## Result

```text
101
Dilip
2022
99.88
true
```

---

# Collection Injection

A bean can also contain collections.

```java
private List<String> emails;

private Set<String> mobileNumbers;

private Map<String,String> subMarks;
```

Spring can inject entire collections using XML.

---

# List Injection

## Situation

A student can have multiple email addresses.

```java
private List<String> emails;
```

---

## Configuration

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

## What Spring Does

```java
List<String> emails =
        new ArrayList<>();

emails.add("dilip@gmail.com");
emails.add("laxmi@gmail.com");
emails.add("dilip@gmail.com");
```

---

## Result

```text
[dilip@gmail.com,
 laxmi@gmail.com,
 dilip@gmail.com]
```

---

## Key Observation

```text
List preserves order.

List allows duplicates.
```

---

# Set Injection

## Situation

Mobile numbers should be unique.

```java
private Set<String> mobileNumbers;
```

---

## Configuration

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

## What Spring Does

```java
Set<String> mobiles =
        new HashSet<>();

mobiles.add("8826111377");
mobiles.add("8826111377");
mobiles.add("+1234567890");
```

---

## Result

```text
[8826111377,
 +1234567890]
```

---

## Key Observation

```text
Set stores unique values.

Duplicate entries are removed.
```

---

# Map Injection

## Situation

Subject names must be associated with marks.

```java
private Map<String,String> subMarks;
```

---

## Configuration

```xml
<property name="subMarks">

    <map>

        <entry key="maths"
               value="88"/>

        <entry key="science"
               value="66"/>

        <entry key="english"
               value="44"/>

    </map>

</property>
```

---

## What Spring Does

```java
Map<String,String> marks =
        new HashMap<>();

marks.put("maths", "88");
marks.put("science", "66");
marks.put("english", "44");
```

---

## Result

```text
{
 maths=88,
 science=66,
 english=44
}
```

---

## Key Observation

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

| Collection | Stores | Duplicates | Order |
|------------|---------|------------|--------|
| List | Values | Allowed | Preserved |
| Set | Values | Not Allowed | Not Guaranteed |
| Map | Key → Value | Keys Unique | Key-Based |

---

## Internal Flow of Collection Injection

```text
XML Collection
        │
        ▼
Spring Creates Collection Object
        │
        ▼
Values Added
        │
        ▼
Collection Assigned To Bean
        │
        ▼
Bean Stored In Container
```

---

# Complete Spring Flow

```text
beans.xml
      │
      ▼
Spring Container Starts
      │
      ▼
Student Object Created
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
Bean Stored In IoC Container
      │
      ▼
getBean()
      │
      ▼
Fully Configured Object Returned
```

---

# Real Usage

### List

```text
Email Addresses

Skills

Course Names
```

### Set

```text
Roles

Permissions

Unique Phone Numbers
```

### Map

```text
Subject → Marks

Product → Price

Country → Capital
```

---

# Key Takeaways

```text
<property>
        ↓
Setter Injection
```

```text
<list>
        ↓
Duplicates Allowed
```

```text
<set>
        ↓
Duplicates Removed
```

```text
<map>
        ↓
Key → Value Storage
```

```text
Spring Reads XML
        │
        ▼
Calls Setter Methods
        │
        ▼
Injects Values
        │
        ▼
Bean Ready
```
