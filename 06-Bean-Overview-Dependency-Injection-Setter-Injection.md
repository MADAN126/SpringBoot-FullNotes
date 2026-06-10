# Bean Overview, Dependency Injection & Setter Injection

## Situation

Consider a Student object.

```java
Student student =
        new Student();
```

The object exists.

But it still needs data.

```text
Student Name

Student Id

College Name
```

The question is:

```text
Who Creates The Object?

Who Supplies The Data?

Who Stores The Object?

Who Returns The Object Later?
```

---

# Bean Overview

## Problem

In large applications there may be hundreds of objects.

Examples:

```text
Student

Employee

Course

Address

Account
```

Creating and managing every object manually becomes difficult.

---

## Solution

Spring manages application objects.

Spring calls these managed objects:

```text
Beans
```

A Bean is simply:

```text
An Object Managed By Spring
```

---

## Creating a Bean

```xml
<bean id="s1"
      class="com.naresh.first.core.Student"/>
```

This tells Spring:

```text
Create Student Object

Manage Student Object

Store Student Object
```

---

## Internal Flow

```text
Bean Definition Found
         │
         ▼
Class Identified
         │
         ▼
Object Created
         │
         ▼
Bean Stored
         │
         ▼
Ready To Use
```

---

## What Spring Does Internally

Spring reads:

```xml
<bean id="s1"
      class="Student"/>
```

and performs:

```java
Student s1 =
        new Student();
```

Then stores it inside the container.

---

# Bean Identifier (id)

## Problem

Suppose Spring creates:

```text
Student

Employee

Address

Course
```

How will the application ask for a specific object?

---

## Solution

Every Bean gets a unique identifier.

Example:

```xml
<bean id="s1"
      class="Student"/>
```

Here:

```text
Bean Id = s1
```

---

## Retrieving Bean

```java
Student student =
(Student)
factory.getBean("s1");
```

Spring searches:

```text
s1
```

and returns the matching object.

---

## Internal Flow

```text
getBean("s1")
        │
        ▼
Container Searches
        │
        ▼
Bean Found
        │
        ▼
Object Returned
```

---

# Bean Naming Convention

Spring bean names usually follow Java variable naming rules.

Examples:

```text
studentService

accountManager

userDao

loginController
```

Rule:

```text
Start With Lowercase

Use camelCase
```

---

# Bean Definition = Object Recipe

## Situation

Before creating an object, Spring needs instructions.

---

## Solution

Bean definition acts as a recipe.

Example:

```xml
<bean id="s1"
      class="Student"/>
```

This definition tells Spring:

```text
Which Class To Create

What Id To Assign
```

---

## Internal Flow

```text
Bean Definition
        │
        ▼
Spring Reads Configuration
        │
        ▼
Object Created
        │
        ▼
Object Stored
```

---

# Dependency Injection (DI)

## Situation

Student needs Address.

```java
Student
    │
    ▼
Address
```

Student depends on Address.

---

## Problem

Without Dependency Injection:

```java
public class Student {

    private Address address;

    public Student() {

        address =
            new Address();
    }
}
```

Student creates Address itself.

Now:

```text
Student Knows Address

Student Creates Address

Student Controls Address
```

Both classes become tightly connected.

---

## Why Is This A Problem?

Suppose Address changes.

```text
Address Modified
        │
        ▼
Student May Need Changes
```

Testing also becomes harder.

Because Student always creates Address.

---

## Solution

Let somebody else provide Address.

```java
public class Student {

    private Address address;

    public void setAddress(
            Address address) {

        this.address =
                address;
    }
}
```

Student no longer creates Address.

Student only receives Address.

---

## Internal Flow

```text
Address Created
       │
       ▼
Student Created
       │
       ▼
Address Supplied
       │
       ▼
Student Ready
```

---

## Result

```text
Loose Coupling
```

Student uses Address.

Student does not create Address.

---

# Why Dependency Injection Exists

Dependency Injection separates:

```text
Object Usage
```

from

```text
Object Creation
```

---

Without DI:

```text
Class Creates Dependency
```

With DI:

```text
Class Receives Dependency
```

---

## Result

```text
Easy Maintenance

Easy Testing

Easy Replacement

Flexible Design
```

---

# Types of Dependency Injection

Spring can inject dependencies in three ways.

| Type | Injection Point |
|--------|--------|
| Setter Injection | Setter Method |
| Constructor Injection | Constructor |
| Field Injection | Direct Field |

---

# Setter Injection

## Situation

Student object is created.

Now Spring must supply values.

```text
Student Name

Student Id

College Name
```

---

## Solution

Spring uses setter methods.

This approach is called:

```text
Setter Injection
```

---

## Student Class

```java
public class Student {

    private String studentName;
    private String studentId;
    private String clgName;

}
```

---

## Bean Configuration

```xml
<bean id="s1"
      class="Student">

    <property
            name="clgName"
            value="ABC College"/>

    <property
            name="studentName"
            value="Dilip Singh"/>

    <property
            name="studentId"
            value="100"/>

</bean>
```

---

# Understanding property Tag

## Problem

Spring must know:

```text
Which Variable?

Which Value?
```

---

## name Attribute

Identifies the target variable.

Example:

```xml
name="studentName"
```

Spring searches for:

```java
private String studentName;
```

and

```java
setStudentName()
```

---

## value Attribute

Represents the actual value.

Example:

```xml
value="Dilip Singh"
```

Spring injects this value into the property.

---

# What Spring Does Internally

Spring creates object first.

```java
Student s1 =
        new Student();
```

Then injects values.

```java
s1.setClgName(
        "ABC College");

s1.setStudentName(
        "Dilip Singh");

s1.setStudentId(
        "100");
```

---

## Important Observation

Setter Injection happens:

```text
After Object Creation
```

First:

```java
new Student()
```

Then:

```java
setStudentName()
```

---

# Getting the Bean

Application requests object.

```java
Student s1 =
(Student)
factory.getBean("s1");
```

Spring returns the configured bean.

---

## Internal Flow

```text
getBean("s1")
       │
       ▼
Container Searches
       │
       ▼
Bean Found
       │
       ▼
Configured Object Returned
```

---

# Complete Internal Flow

```text
beans.xml
      │
      ▼
Spring Reads Configuration
      │
      ▼
Student Object Created
      │
      ▼
Setter Methods Invoked
      │
      ▼
Values Injected
      │
      ▼
Bean Stored In Container
      │
      ▼
getBean("s1")
      │
      ▼
Configured Bean Returned
```

---

# Output

```text
100

Dilip Singh

ABC College

This is Student class

76.0
```

---

# Key Observation

A Bean is:

```text
An Object Managed By Spring
```

Dependency Injection means:

```text
Object Receives Dependency

Instead Of

Creating Dependency
```

Setter Injection means:

```text
Create Object First
         │
         ▼
Inject Values Later
```

Spring achieves this using:

```xml
<property>
```

and internally invokes the corresponding setter methods.
