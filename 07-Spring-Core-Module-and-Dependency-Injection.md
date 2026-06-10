# 07. Spring Core Container 

## Spring Core Container

Spring Core Container consists of three major modules:

```text
Spring Core
      +
Spring Beans
      +
Spring Context
```

Together they form the foundation of the Spring Framework.

---

## 1. Spring Core Module

### Purpose

Spring Core provides the fundamental concepts of Spring.

Main Concepts:

- Inversion of Control (IoC)
- Dependency Injection (DI)

### Responsibility

Spring Core defines the rules and mechanism through which Spring manages objects and dependencies.

### Think Of It As

```text
Spring Core = Brain
```

It defines:

- How objects should be managed
- How dependencies should be injected
- How IoC should work

### Key Point

```text
Spring Core provides IoC and DI concepts.
```

---

## 2. Spring Beans Module

### Purpose

Spring Beans module is responsible for Bean creation and Bean lifecycle management.

It contains:

- BeanFactory
- BeanDefinition
- Bean Lifecycle Management

### Example

```xml
<bean id="s1"
      class="com.naresh.first.core.Student"/>
```

Spring reads this configuration and creates the corresponding object.

### Responsibility

- Bean Creation
- Bean Configuration
- Bean Lifecycle Management

### Think Of It As

```text
Spring Core  = Rules

Spring Beans = Bean Manager
```

### Key Point

```text
Spring Beans module creates and manages Beans.
```

---

## 3. Spring Context Module

### Purpose

Spring Context provides an advanced container called ApplicationContext.

It is built on top of Spring Core and Spring Beans modules.

### Responsibility

- Accessing Beans
- Managing Bean Relationships
- Event Handling
- Resource Loading
- Application Configuration

### Example

```java
ApplicationContext context =
new ClassPathXmlApplicationContext("beans.xml");
```

### Think Of It As

```text
Spring Context = Interface To Spring Container
```

### Key Point

```text
Spring Context provides access to configured Beans.
```

---

# Relationship Between Modules

```text
Spring Core
      ↓
Provides IoC & DI Concepts

Spring Beans
      ↓
Creates & Manages Beans

Spring Context
      ↓
Provides Access To Beans
```

---

# Quick Revision

| Module | Responsibility |
|----------|----------------|
| Spring Core | Provides IoC and DI concepts |
| Spring Beans | Creates and manages Beans |
| Spring Context | Provides access to Beans through ApplicationContext |

---

# Interview Answer

### What is Spring Core Container?

Spring Core Container is the foundation of the Spring Framework and consists of three modules:

1. Spring Core → Provides IoC and Dependency Injection concepts.
2. Spring Beans → Responsible for Bean creation, configuration, and lifecycle management.
3. Spring Context → Provides ApplicationContext to access and manage Beans.

---

# Memory Trick

```text
Core    → Concepts

Beans   → Creation

Context → Access
```

### One-Line Summary

```text
Spring Core defines IoC and DI concepts,
Spring Beans creates and manages objects,
Spring Context provides access to those objects.
```
