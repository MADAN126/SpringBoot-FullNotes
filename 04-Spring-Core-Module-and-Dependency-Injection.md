# Spring Core Module and Dependency Injection

## Introduction

The Spring Core Module is the foundation of the Spring Framework.

It provides the fundamental features required for Spring applications, including:

- Inversion of Control (IoC)
- Dependency Injection (DI)
- Bean Management
- Application Context

Most of Spring's powerful features are built on top of the Core Module.

---

# Spring Core Container

The Spring Core Module consists of three important parts:

## 1. Spring Core

Spring Core is the heart of the Spring Framework.

It provides support for:

- Inversion of Control (IoC)
- Dependency Injection (DI)
- Singleton Design Pattern

### Responsibility

Spring Core manages object creation and dependency management.

Instead of developers creating and managing objects manually, Spring takes care of it.

---

## 2. Spring Bean

Spring Bean module provides object management using the BeanFactory mechanism.

A Bean is simply an object managed by the Spring IoC Container.

### Responsibility

- Create Objects
- Configure Objects
- Manage Object Lifecycle
- Handle Dependencies

---

## 3. Spring Context

Spring Context is built on top of Core and Bean modules.

It acts as a container that provides access to all configured Spring objects.

### Responsibility

- Stores Spring Beans
- Manages Bean Relationships
- Provides Application Configuration

---

# Understanding Spring Bean

## What is a Bean?

A Bean is a Java object managed by the Spring IoC Container.

### Simple Definition

> Any object created, configured, and managed by Spring is called a Spring Bean.

---

## Traditional Java Object

```java
Employee emp = new Employee();
```

Here, the developer creates and manages the object manually.

---

## Spring Bean

```java
@Component
public class Employee {
}
```

Spring automatically creates and manages the object.

### Benefit

Developers do not need to worry about object creation and lifecycle management.

---

# Dependency Injection (DI)

## What is Dependency?

A dependency exists when one class requires another class to perform its work.

### Example

Consider two classes:

```text
Employee
    ↓
Address
```

Employee uses Address.

Therefore:

> Employee depends on Address.

---

## Real-Life Example

A Car depends on an Engine.

Without an Engine:

```text
Car ❌ Cannot Function
```

With an Engine:

```text
Car ✅ Can Function
```

Similarly:

```text
Employee
    ↓
Address
```

Employee requires Address to function properly.

---

# Traditional Approach

Without Dependency Injection:

```java
public class Employee {

    private Address addr;

    public Employee() {
        this.addr = new Address();
    }
}
```

### Problem

Employee creates Address itself.

This causes:

- Tight Coupling
- Difficult Testing
- Difficult Maintenance

---

## Why is This a Problem?

Suppose Address changes.

```java
Address address = new Address();
```

Every dependent class may need modification.

As applications grow larger, maintenance becomes difficult.

---

# Dependency Injection Approach

Using Dependency Injection:

```java
public class Employee {

    private Address addr;

    public Employee(Address addr) {
        this.addr = addr;
    }
}
```

### What Changed?

Employee no longer creates Address.

Instead:

- Address is provided from outside.
- Spring supplies the required dependency.

---

## Dependency Flow

Without DI:

```text
Employee
    ↓ Creates
Address
```

With DI:

```text
Spring Container
      ↓ Injects
Employee
      ↓ Uses
Address
```

---

# How Spring Performs Dependency Injection

Spring can inject dependencies using:

## Constructor Injection

```java
public Employee(Address addr) {
    this.addr = addr;
}
```

Recommended approach in most applications.

---

## Setter Injection

```java
public void setAddress(Address addr) {
    this.addr = addr;
}
```

Dependency is provided through setter methods.

---

## Field Injection

```java
@Autowired
private Address addr;
```

Spring injects the dependency directly into the field.

---

# Relationship Between IoC and DI

Many beginners confuse these concepts.

## Inversion of Control (IoC)

IoC is the principle.

> Spring controls object creation and management.

---

## Dependency Injection (DI)

DI is the technique used to achieve IoC.

> Spring injects required dependencies into objects.

### Simple Formula

```text
IoC = Principle

DI = Implementation Technique
```

---

# Benefits of Dependency Injection

- Loose Coupling
- Better Code Reusability
- Easier Testing
- Easier Maintenance
- Better Scalability
- Cleaner Design

---

# Key Points to Remember

- Spring Core Module is the foundation of Spring Framework.
- Spring Core provides IoC and DI.
- Spring Bean represents objects managed by Spring.
- Spring Context provides access to configured beans.
- Dependency exists when one class uses another class.
- Dependency Injection means providing dependencies from outside.
- Spring supports Constructor, Setter, and Field Injection.
- DI helps achieve Loose Coupling.
- IoC is the concept, and DI is the implementation technique.

---

# Conclusion

The Spring Core Module provides the foundation for Spring Framework through IoC, Dependency Injection, Bean Management, and Application Context. Dependency Injection allows Spring to provide required objects automatically, resulting in loosely coupled, maintainable, and scalable applications.
