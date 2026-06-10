# 07. Spring Core Module and Dependency Injection

## Introduction

The **Spring Core Module** is the foundation of the Spring Framework.

It provides the fundamental features required for building Spring applications.

Most of Spring's advanced features are built on top of the Core Module.

### Core Responsibilities

- Inversion of Control (IoC)
- Dependency Injection (DI)
- Bean Management
- Application Context

Without the Core Module, the Spring Framework cannot function.

---

# Spring Core Container

The Spring Core Module consists of three major components:

```text
Spring Core Container
        │
 ┌──────┼──────┐
 │      │      │
Core   Bean  Context
```

---

# 1. Spring Core

## What is Spring Core?

Spring Core is the heart of the Spring Framework.

It provides the implementation for:

- Inversion of Control (IoC)
- Dependency Injection (DI)
- Singleton Design Pattern

---

## Main Responsibility

Spring Core manages:

- Object Creation
- Object Configuration
- Dependency Management

---

## Traditional Java

```java
Student s = new Student();
```

Developer creates and manages objects.

---

## Spring Approach

```text
Spring Container
        ↓
Creates Object
        ↓
Manages Object
```

Object management responsibility is transferred to Spring.

---

## Why Spring Core is Important?

Without Spring Core:

```text
Developer
      ↓
Create Objects
      ↓
Manage Dependencies
      ↓
Handle Lifecycle
```

With Spring Core:

```text
Spring
      ↓
Creates Objects
      ↓
Injects Dependencies
      ↓
Manages Lifecycle
```

Development becomes simpler and cleaner.

---

# 2. Spring Bean

## What is a Spring Bean?

A Bean is a Java object managed by the Spring IoC Container.

### Simple Definition

> Any object created, configured, and managed by Spring is called a Spring Bean.

---

## Traditional Java Object

```java
Employee emp = new Employee();
```

Developer creates the object.

---

## Spring Bean

```java
@Component
public class Employee {
}
```

Spring creates and manages the object automatically.

---

## Responsibilities of Spring Bean Module

- Create Objects
- Configure Objects
- Store Objects
- Manage Object Lifecycle
- Handle Dependencies

---

## Important Point

Not every Java object is a Bean.

### Example

```java
Student s = new Student();
```

Normal Java Object.

---

```java
@Component
public class Student {
}
```

Spring Bean.

---

# 3. Spring Context

## What is Spring Context?

Spring Context is built on top of:

- Spring Core
- Spring Bean

It acts as the central container that provides access to all configured Spring Beans.

---

## Responsibilities

### Store Beans

```text
Student Bean
Employee Bean
Address Bean
```

All are stored inside the Context.

---

### Manage Relationships

```text
Employee
     ↓
Address
```

Spring Context manages the relationship.

---

### Provide Configuration

Spring Context reads configuration from:

- XML
- Annotations
- Java Configuration

---

## Practical View

Think of Spring Context as:

```text
Bean Warehouse
```

All Beans live inside the Context.

Whenever an application requests a Bean:

```java
factory.getBean("student");
```

Spring Context returns it.

---

# Understanding Dependency

## What is a Dependency?

A dependency exists when one class requires another class to perform its work.

---

## Example

```text
Employee
    ↓
Address
```

Employee uses Address.

Therefore:

```text
Employee depends on Address
```

---

## Real-Life Example

```text
Car
 ↓
Engine
```

Without Engine:

```text
Car ❌ Cannot Function
```

With Engine:

```text
Car ✅ Can Function
```

Similarly:

```text
Employee
 ↓
Address
```

Employee requires Address.

---

# Dependency Injection (DI)

## What is Dependency Injection?

Dependency Injection is a technique where required objects are provided from outside instead of being created inside the class.

---

## Traditional Approach

```java
public class Employee {

    private Address addr;

    public Employee() {
        this.addr = new Address();
    }
}
```

---

## Problem

Employee creates Address itself.

```text
Employee
     ↓ Creates
Address
```

This leads to:

- Tight Coupling
- Difficult Testing
- Difficult Maintenance

---

## Why is Tight Coupling Bad?

Suppose Address class changes.

```java
Address addr = new Address();
```

Every dependent class may require modification.

As the application grows:

```text
More Dependencies
        ↓
More Changes
        ↓
More Maintenance Cost
```

---

# Dependency Injection Solution

## Using DI

```java
public class Employee {

    private Address addr;

    public Employee(Address addr) {
        this.addr = addr;
    }
}
```

---

## What Changed?

Employee no longer creates Address.

Instead:

```text
Spring Provides Address
```

Employee simply uses it.

---

## Dependency Flow

### Without DI

```text
Employee
     ↓ Creates
Address
```

---

### With DI

```text
Spring Container
        ↓ Injects
Employee
        ↓ Uses
Address
```

---

# How Spring Performs Dependency Injection

Spring supports three injection methods.

---

# 1. Constructor Injection

Dependency is supplied through constructor.

```java
public Employee(Address addr) {
    this.addr = addr;
}
```

---

## Flow

```text
Spring
   ↓
Creates Address
   ↓
Calls Constructor
   ↓
Creates Employee
```

---

## Advantages

- Recommended approach
- Ensures mandatory dependencies
- Better immutability

---

# 2. Setter Injection

Dependency is supplied through setter methods.

```java
public void setAddress(Address addr) {
    this.addr = addr;
}
```

---

## Flow

```text
Spring
   ↓
Creates Employee
   ↓
Calls setAddress()
   ↓
Injects Address
```

---

## Advantages

- Flexible
- Suitable for optional dependencies

---

# 3. Field Injection

Dependency is injected directly into fields.

```java
@Autowired
private Address addr;
```

---

## Flow

```text
Spring
   ↓
Creates Object
   ↓
Injects Field Directly
```

---

## Note

Field Injection is simple but Constructor Injection is generally preferred in modern Spring applications.

---

# Relationship Between IoC and DI

Many beginners confuse these two concepts.

---

## Inversion of Control (IoC)

IoC is the principle.

It says:

```text
Spring Should Control
Object Creation
And Object Management
```

---

## Dependency Injection (DI)

DI is the technique used to achieve IoC.

Spring injects required dependencies automatically.

---

## Simple Formula

```text
IoC = Concept / Principle

DI = Implementation Technique
```

---

## Easy Way to Remember

### IoC

```text
Who Controls Objects?
```

Answer:

```text
Spring
```

---

### DI

```text
How Are Dependencies Supplied?
```

Answer:

```text
Through Injection
```

---

# Benefits of Dependency Injection

## 1. Loose Coupling

Components become independent.

```text
Employee
     ↓
Interface
     ↓
Address
```

Changes become easier.

---

## 2. Better Reusability

Classes can be reused in different applications.

---

## 3. Easier Testing

Mock objects can be injected.

Example:

```java
MockAddress
```

instead of:

```java
RealAddress
```

---

## 4. Easier Maintenance

Dependencies can change without changing business logic.

---

## 5. Better Scalability

Large applications become easier to manage.

---

## 6. Cleaner Design

Business logic remains separate from object creation logic.

---

# Internal Working of Spring DI

```text
Application Starts
        ↓
Spring Context Loads
        ↓
Bean Definitions Read
        ↓
Objects Created
        ↓
Dependencies Injected
        ↓
Beans Stored in Container
        ↓
Application Requests Bean
        ↓
Spring Returns Ready Object
```

---

# Key Points

- Spring Core Module is the foundation of Spring Framework.
- Spring Core provides IoC and Dependency Injection.
- Spring Bean represents objects managed by Spring.
- Spring Context acts as the central container.
- A dependency exists when one class uses another class.
- Dependency Injection means supplying dependencies from outside.
- Spring supports Constructor, Setter, and Field Injection.
- Constructor Injection is the most preferred approach.
- DI helps achieve Loose Coupling.
- IoC is the concept, and DI is the implementation technique.

---

# Interview Questions

### What is Spring Core Module?

The foundational module of Spring that provides IoC, DI, Bean Management, and Application Context support.

---

### What is a Spring Bean?

A Java object created, configured, and managed by the Spring IoC Container.

---

### What is Dependency Injection?

A technique where dependencies are provided from outside instead of being created inside a class.

---

### What are the types of Dependency Injection?

- Constructor Injection
- Setter Injection
- Field Injection

---

### Difference Between IoC and DI?

```text
IoC = Principle

DI = Technique Used To Implement IoC
```

---

# Conclusion

The Spring Core Module forms the backbone of the Spring Framework by providing essential features such as IoC, Dependency Injection, Bean Management, and Application Context. Dependency Injection allows Spring to automatically provide required objects, reducing tight coupling and improving maintainability, scalability, reusability, and testability. Understanding Spring Core and DI is crucial because almost every Spring feature ultimately relies on these foundational concepts.
