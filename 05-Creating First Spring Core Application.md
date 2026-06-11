# Creating First Spring Core Application

## Situation

We have a Java class.

```java
public class Student {

    private String studentName;

}
```

Now the question is:

```text
Who Will Create Student Object?

Who Will Store It?

Who Will Return It When Needed?
```

In normal Java:

```java
Student s =
        new Student();
```

Developer creates the object.

---

## Problem

In real projects:

```text
Student

Employee

Course

Account

Address
```

Hundreds of objects may exist.

Manually managing every object becomes difficult.

Developer must:

```text
Create Objects

Store Objects

Manage Dependencies

Maintain Libraries
```

This increases complexity.

---

## Solution

Spring introduces:

```text
IoC Container
```

The container takes responsibility for:

```text
Creating Objects

Managing Objects

Storing Objects

Providing Objects
```

Developer only describes:

```text
What To Create
```

Spring does the rest.

---

# Project Creation Approaches

## Manual JAR Configuration

### How It Works

```text
Create Java Project
        │
        ▼
Download Spring JARs
        │
        ▼
Add JARs To Build Path
        │
        ▼
Configure Dependencies
```

---

### Problem

Developer manages everything manually.

```text
More Setup

More Maintenance

Dependency Conflicts Possible
```

---

## Maven Configuration

### How It Works

```text
Create Maven Project
        │
        ▼
Add Dependency
        │
        ▼
Maven Downloads JARs
        │
        ▼
Project Ready
```

---

### Why Preferred?

Developer only writes:

```xml
<dependency>
    ...
</dependency>
```

Maven handles:

```text
Download

Version Management

Dependency Resolution
```

---

# Step 1: Create POJO Class

## Situation

Spring needs a class to manage.

---

## Code

```java
public class Student {

    private String studentName;

    public String getStudentName() {
        return studentName;
    }

    public void setStudentName(
            String studentName) {

        this.studentName =
                studentName;
    }
}
```

---

## What Is This Class?

This is a:

```text
POJO
```

Meaning:

```text
Plain Old Java Object
```

A normal Java class without Spring code.

---

## Key Observation

Spring does not require special classes.

It can manage:

```text
Normal Java Objects
```

---

# Step 2: Create Configuration File

## Situation

Spring needs instructions.

Without instructions Spring doesn't know:

```text
Which Class?

Which Object?

Which Name?
```

---

## Solution

Create:

```text
beans.xml
```

This file acts as:

```text
Spring Configuration Center
```

---

## XML Structure

```xml
<beans>

</beans>
```

Everything Spring should manage is defined inside:

```xml
<beans>
```

---

# Why beans.xml Exists

Instead of writing:

```java
new Student();
```

we describe the object in XML.

Spring reads XML and creates the object.

---

## Flow

```text
beans.xml
      │
      ▼
Spring Reads Instructions
      │
      ▼
Object Creation Starts
```

---

# Step 3: Configure Bean

## Situation

Spring knows configuration file exists.

But still doesn't know which class to create.

---

## Solution

Register the class.

```xml
<bean id="stu"
      class="com.naresh.hello.Student"/>
```

---

# Understanding Bean Configuration

## id Attribute

```xml
id="stu"
```

Represents:

```text
Bean Name
```

Used later to retrieve the object.

---

### Example

```java
getBean("stu");
```

Spring searches:

```text
stu
```

and returns that object.

---

## class Attribute

```xml
class="com.naresh.hello.Student"
```

Represents:

```text
Fully Qualified Class Name
```

Format:

```text
packageName.className
```

---

## What Spring Understands

From:

```xml
<bean id="stu"
      class="Student"/>
```

Spring understands:

```text
Create Student Object

Store As "stu"
```

---

# Step 4: Create Spring Container

## Code

```java
BeanFactory factory =
new FileSystemXmlApplicationContext(
        "beans.xml");
```

---

## What Happens Here?

Spring starts.

Reads:

```text
beans.xml
```

Creates all configured beans.

Stores them inside container.

---

## Internal Flow

```text
Application Starts
        │
        ▼
Spring Container Created
        │
        ▼
beans.xml Loaded
        │
        ▼
Bean Definitions Read
        │
        ▼
Objects Created
        │
        ▼
Objects Stored
```

---

# Getting Bean Object

## Code

```java
Student s =
(Student)
factory.getBean("stu");
```

---

## What Is Happening?

Application requests:

```text
stu
```

Spring searches container.

Returns matching object.

---

## Internal Flow

```text
getBean("stu")
        │
        ▼
Search Bean Id
        │
        ▼
Bean Found
        │
        ▼
Return Student Object
```

---

# Most Important Observation

Notice:

```java
Student s =
(Student)
factory.getBean("stu");
```

We never wrote:

```java
new Student();
```

---

## Traditional Java

Developer controls creation.

```java
Student s =
        new Student();
```

Flow:

```text
Developer
      │
      ▼
Creates Object
```

---

## Spring

Container controls creation.

```java
Student s =
(Student)
factory.getBean("stu");
```

Flow:

```text
Developer
      │
      ▼
Requests Object
      │
      ▼
Spring Returns Object
```

---

# What Is Actually Inverted?

Normally:

```text
Developer Creates Objects
```

With Spring:

```text
Spring Creates Objects
```

Control moves from:

```text
Developer
```

to

```text
Spring Container
```

This is the idea behind:

```text
Inversion Of Control (IoC)
```

---

# Complete Internal Execution

Spring reads:

```xml
<bean id="stu"
      class="Student"/>
```

Internally performs:

```java
Student stu =
        new Student();
```

Then stores:

```text
stu
        │
        ▼
IoC Container
```

Later:

```java
getBean("stu");
```

returns the same object.

---

# Multiple Beans Of Same Class

## Situation

Same class may represent different objects.

---

## Configuration

```xml
<bean id="s1"
      class="Student"/>

<bean id="s2"
      class="Student"/>
```

---

## What Spring Creates

```text
Student Object
        │
        ├── s1

        └── s2
```

Same class.

Different bean identities.

---

## Internal Flow

```text
Bean Definition s1
        │
        ▼
Student Object Created

Bean Definition s2
        │
        ▼
Student Object Created
```

---

# End-to-End Flow

```text
Student Class
       │
       ▼
beans.xml
       │
       ▼
Bean Definition Added
       │
       ▼
Spring Container Starts
       │
       ▼
Configuration Read
       │
       ▼
Student Object Created
       │
       ▼
Bean Stored As "stu"
       │
       ▼
getBean("stu")
       │
       ▼
Student Object Returned
```

---

# Key Observation

Spring Core application is fundamentally doing only one thing:

```text
Developer Describes Object

Spring Creates Object

Spring Stores Object

Spring Returns Object
```

The first Spring application is not about XML.

It is not about BeanFactory.

It is about understanding:

```text
Object Creation Responsibility
```

Traditional Java:

```text
Developer Creates Object
```

Spring:

```text
Container Creates Object
```

That single shift is the foundation of:

```text
IoC

Dependency Injection

Bean Management
```
