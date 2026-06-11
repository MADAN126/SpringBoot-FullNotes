# Spring Core Module and Dependency Injection

## Situation

A real application contains many objects.

```text
Employee

Address

Account

Course

Student
```

Now the questions are:

```text
Who Creates These Objects?

Who Stores Them?

Who Connects Them Together?

Who Returns Them When Needed?
```

Managing everything manually becomes difficult as the application grows.

---

## Problem

Traditional Java applications require developers to manage objects directly.

Example:

```java
Employee emp =
        new Employee();

Address addr =
        new Address();
```

Developer is responsible for:

```text
Object Creation

Object Management

Dependency Management

Object Relationships
```

As the number of classes increases:

```text
More Code

More Maintenance

More Coupling
```

---

## Solution

Spring introduces:

```text
Spring Core Container
```

The container takes responsibility for:

```text
Creating Objects

Managing Objects

Connecting Objects

Providing Objects
```

Developer focuses on:

```text
Business Logic
```

while Spring handles infrastructure work.

---

# Spring Core Container

The Spring Core Container is built using three major modules.

```text
Spring Core
      │
      ▼
Spring Beans
      │
      ▼
Spring Context
```

Together they provide object management in Spring.

---

# 1. Spring Core

## Situation

Spring must know:

```text
How Objects Should Be Created

How Dependencies Should Be Supplied
```

---

## Solution

Spring Core provides the rules.

It introduces:

```text
IoC

Dependency Injection
```

---

## What Spring Core Does

```text
Defines Object Management Rules

Defines Dependency Injection Rules

Provides IoC Mechanism
```

---

## Internal View

Think of it as:

```text
Spring Core
      =
Framework Brain
```

It decides:

```text
How Spring Should Manage Objects
```

---

# 2. Spring Beans

## Situation

Knowing the rules is not enough.

Spring must actually create objects.

---

## Solution

Spring Beans module performs object management.

---

## What Spring Beans Does

```text
Create Objects

Configure Objects

Manage Object Lifecycle

Handle Dependencies
```

---

## Example

```xml
<bean id="emp"
      class="Employee"/>
```

Spring Beans module reads this configuration.

Then internally performs:

```java
new Employee();
```

---

## Internal Flow

```text
Bean Definition
        │
        ▼
Class Identified
        │
        ▼
Object Created
        │
        ▼
Object Stored
```

---

## Key Observation

A Bean is simply:

```text
An Object Managed By Spring
```

---

# 3. Spring Context

## Situation

Objects have been created.

Now the application needs access to them.

---

## Solution

Spring Context provides access to all managed objects.

---

## What Spring Context Does

```text
Stores Beans

Manages Bean Relationships

Provides Application Configuration

Returns Beans When Requested
```

---

## Example

```java
ApplicationContext context =
new ClassPathXmlApplicationContext(
        "beans.xml");
```

Now beans can be accessed using:

```java
context.getBean("emp");
```

---

## Internal Flow

```text
Application
       │
       ▼
ApplicationContext
       │
       ▼
Search Bean
       │
       ▼
Return Bean
```

---

# Relationship Between Core, Beans and Context

```text
Spring Core
       │
       ▼
Provides IoC & DI Rules

Spring Beans
       │
       ▼
Creates And Manages Objects

Spring Context
       │
       ▼
Provides Access To Objects
```

---

# Understanding Spring Bean

## Situation

Spring creates an object.

Example:

```java
Employee emp =
        new Employee();
```

Now Spring stores and manages this object.

---

## Result

That object becomes:

```text
Spring Bean
```

---

## Key Observation

Not every Java object is a Bean.

```text
Java Object
      +
Managed By Spring
      =
Spring Bean
```

---

# Traditional Object vs Spring Bean

## Traditional Java

```java
Employee emp =
        new Employee();
```

Developer:

```text
Creates Object

Stores Object

Manages Object
```

---

## Spring

```java
@Component
public class Employee {
}
```

Spring:

```text
Creates Object

Stores Object

Manages Object
```

---

## Flow

```text
Class Found
      │
      ▼
Spring Creates Object
      │
      ▼
Object Managed By Container
      │
      ▼
Becomes Bean
```

---

# Dependency Injection (DI)

## Situation

Employee needs Address.

```text
Employee
     │
     ▼
Address
```

Employee depends on Address.

---

## What Is A Dependency?

When one class requires another class to perform its work:

```text
Dependency Exists
```

Example:

```text
Employee Uses Address

Employee Depends On Address
```

---

# Real World Example

```text
Car
 │
 ▼
Engine
```

Without Engine:

```text
Car Cannot Work
```

Similarly:

```text
Employee
 │
 ▼
Address
```

Employee needs Address.

---

# Traditional Approach

## Code

```java
public class Employee {

    private Address addr;

    public Employee() {

        addr =
            new Address();
    }
}
```

---

## What Happens?

Employee creates Address itself.

Flow:

```text
Employee
     │
Creates
     ▼
Address
```

---

## Problem

Employee is tightly connected to Address.

Changes become harder.

Testing becomes harder.

Replacing Address becomes harder.

---

# Dependency Injection Approach

## Code

```java
public class Employee {

    private Address addr;

    public Employee(
            Address addr) {

        this.addr = addr;
    }
}
```

---

## What Changed?

Employee no longer creates Address.

Employee only receives Address.

---

## Flow

```text
Address Created
        │
        ▼
Address Supplied
        │
        ▼
Employee Uses Address
```

---

## Key Observation

Employee focuses on:

```text
Using Address
```

not

```text
Creating Address
```

---

# How Spring Performs DI

## Step 1

Spring creates dependency.

```java
Address addr =
        new Address();
```

---

## Step 2

Spring creates Employee.

```java
Employee emp =
        new Employee(addr);
```

---

## Result

Dependency automatically connected.

---

## Complete Flow

```text
Address Bean Created
         │
         ▼
Employee Bean Created
         │
         ▼
Address Injected
         │
         ▼
Employee Ready
```

---

# Types of Dependency Injection

## Constructor Injection

Dependency supplied during object creation.

```java
public Employee(
        Address addr) {

    this.addr = addr;
}
```

---

## Internal Flow

```text
Dependency Created
        │
        ▼
Constructor Called
        │
        ▼
Object Ready
```

---

## Setter Injection

Dependency supplied after object creation.

```java
public void setAddress(
        Address addr) {

    this.addr = addr;
}
```

---

## Internal Flow

```text
Object Created
        │
        ▼
Setter Called
        │
        ▼
Dependency Injected
```

---

## Field Injection

Dependency injected directly into field.

```java
@Autowired
private Address addr;
```

---

## Internal Flow

```text
Object Created
        │
        ▼
Spring Finds Field
        │
        ▼
Dependency Injected
```

---

# IoC vs DI

Many beginners treat them as the same thing.

They are related but different.

---

## IoC

### Question

Who controls object creation?

---

### Traditional Java

```text
Developer Creates Objects
```

---

### Spring

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

This idea is:

```text
Inversion Of Control
```

---

# DI

### Question

How does Spring connect objects?

---

### Answer

By supplying dependencies automatically.

Example:

```java
Employee(Address addr)
```

Spring provides:

```java
addr
```

This technique is:

```text
Dependency Injection
```

---

# Relationship Between IoC and DI

```text
IoC
 │
 ▼
Spring Controls Objects

DI
 │
 ▼
Spring Connects Objects
```

Or simply:

```text
IoC
=
Goal

DI
=
Mechanism Used To Achieve It
```

---

# End-To-End Flow

```text
Application Starts
         │
         ▼
Spring Container Starts
         │
         ▼
Bean Definitions Found
         │
         ▼
Objects Created
         │
         ▼
Dependencies Created
         │
         ▼
Dependencies Injected
         │
         ▼
Beans Stored
         │
         ▼
Application Requests Bean
         │
         ▼
Spring Returns Bean
```

---

# Key Observation

Spring Core is fundamentally solving one problem:

```text
How To Manage Objects Efficiently
```

It does this by:

```text
Creating Objects

Managing Objects

Connecting Objects

Providing Objects
```

The heart of Spring Core is:

```text
IoC
      +
Dependency Injection
```

because once Spring controls object creation and dependency management, applications become:

```text
Loosely Coupled

Easier To Test

Easier To Maintain

Easier To Scale
```
