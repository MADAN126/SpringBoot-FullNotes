# 08. Injecting Primitive, String and Collection Properties

## Concept

Setter Injection is not limited to objects.

Spring can also inject:

- Primitive values
- String values
- Collections

into bean properties using setter methods.

---

## Why Do We Need This?

Consider a Student Management Application.

Every student object requires:

- Student ID
- Name
- Percentage
- Year
- Selected Status

Instead of writing:

```java
Student s = new Student();
s.setStudId(101);
s.setStuName("Dilip");
```

for every object,

Spring performs the injection automatically from XML configuration.

---

## Primitive and String Injection

### Student Class

```java
public class Student {

    private String stuName;
    private int studId;
    private double avgOfMarks;
    private short passedOutYear;
    private boolean isSelected;

    // setters and getters
}
```

---

### XML Configuration

```xml
<bean id="studentOne"
      class="com.naresh.hello.Student">

    <property name="stuName" value="Dilip"/>
    <property name="studId" value="101"/>
    <property name="avgOfMarks" value="99.88"/>
    <property name="passedOutYear" value="2022"/>
    <property name="isSelected" value="true"/>

</bean>
```

---

## How Spring Executes Internally

Spring performs:

```java
Student studentOne = new Student();

studentOne.setStuName("Dilip");
studentOne.setStudId(101);
studentOne.setAvgOfMarks(99.88);
studentOne.setPassedOutYear((short)2022);
studentOne.setSelected(true);
```

### Important

Spring automatically converts:

```text
"101"    → int
"99.88"  → double
"2022"   → short
"true"   → boolean
```

This conversion is called:

```text
Type Conversion
```

---

## Property Tag

### Syntax

```xml
<property name="variableName"
          value="valueToInject"/>
```

### Meaning

| Attribute | Purpose |
|------------|----------|
| name | Bean property name |
| value | Value to inject |

---

## Real Project Example

Employee Application

```java
private int empId;
private String empName;
private double salary;
```

Spring can load these values directly from XML instead of hardcoding them in Java classes.

This makes configuration changes easier without modifying source code.

---

# Collection Injection

## Why Collection Injection?

Many applications store multiple values:

### Examples

Emails

```java
[dilip@gmail.com,
laxmi@gmail.com]
```

Mobile Numbers

```java
[8826111377,
1234567890]
```

Marks

```java
Maths → 88
Science → 66
```

Spring allows these collections to be injected directly.

---

# List Injection

## Bean Property

```java
private List<String> emails;
```

---

## XML Configuration

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

## Observation

List preserves:

- Insertion Order
- Duplicate Values

---

## Practical Use

Used for:

- Email lists
- Course names
- Product names
- User roles

---

# Set Injection

## Bean Property

```java
private Set<String> mobileNumbers;
```

---

## XML Configuration

```xml
<property name="mobileNumbers">
    <set>
        <value>8826111377</value>
        <value>8826111377</value>
        <value>1234567890</value>
    </set>
</property>
```

---

## Output

```text
[8826111377,
 1234567890]
```

---

## Observation

Set automatically removes duplicates.

---

## Practical Use

Used for:

- Unique phone numbers
- Unique IDs
- Unique usernames

---

# Map Injection

## Bean Property

```java
private Map<String,String> subMarks;
```

---

## XML Configuration

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

## Output

```text
{
 maths=88,
 science=66,
 english=44
}
```

---

## Observation

Map stores data in:

```text
Key → Value
```

format.

---

## Practical Use

Used for:

- Subject → Marks
- Product → Price
- User → Role
- Country → Capital

---

# Collection Comparison

| Feature | List | Set | Map |
|----------|--------|--------|--------|
| Stores Duplicates | Yes | No | Keys No |
| Maintains Order | Yes | Usually Yes | Depends |
| Key-Value Pair | No | No | Yes |
| Most Used For | Multiple Values | Unique Values | Lookup Data |

---

# Common Mistakes

### Mistake 1

Wrong property name

```xml
<property name="studentName"/>
```

when bean variable is

```java
private String stuName;
```

Result:

```text
BeanCreationException
```

---

### Mistake 2

Setter method missing

Spring cannot inject value if setter is absent.

---

### Mistake 3

Wrong data type

```xml
<property name="studId"
          value="ABC"/>
```

for an int field.

Result:

```text
TypeMismatchException
```

---

# Interview Questions

### Q1. Which tag is used for primitive injection?

```xml
<property>
```

---

### Q2. Which tag injects List?

```xml
<list>
```

---

### Q3. Which tag injects Set?

```xml
<set>
```

---

### Q4. Which tag injects Map?

```xml
<map>
```

---

### Q5. How does Spring inject values?

Using setter methods internally.

---

# Quick Revision

| Property Type | XML Tag |
|---------------|----------|
| Primitive | <property> |
| String | <property> |
| List | <list> |
| Set | <set> |
| Map | <map> |

### Remember

List  → Duplicates Allowed

Set   → Duplicates Removed

Map   → Key-Value Storage

Spring Setter Injection =

```text
Create Object
      ↓
Call Setter Methods
      ↓
Inject Values
      ↓
Ready Bean
```
