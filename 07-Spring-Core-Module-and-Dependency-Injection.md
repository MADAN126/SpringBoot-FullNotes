# Spring Core Container

## Situation

Spring is responsible for:

```text
Creating Objects

Managing Objects

Connecting Objects

Providing Objects
```

To perform all these tasks, Spring needs an internal system.

That system is called:

```text
Spring Core Container
```

---

## Problem

Suppose an application contains:

```text
Student

Employee

Address

Course

Account
```

Questions arise:

```text
Who Creates These Objects?

Who Stores Them?

Who Connects Dependencies?

Who Returns Them When Needed?
```

Spring needs dedicated modules to handle these responsibilities.

---

## Solution

Spring Core Container is divided into three modules.

```text
Spring Core
      │
      ▼
Spring Beans
      │
      ▼
Spring Context
```

Together they perform object management.

---

# Big Picture

```text
Spring Core
      │
      ▼
Defines How Spring Should Work
      │
      ▼
Spring Beans
      │
      ▼
Creates And Manages Objects
      │
      ▼
Spring Context
      │
      ▼
Provides Access To Objects
```

---

# 1. Spring Core Module

## Situation

Spring must manage objects automatically.

Before doing that, Spring needs rules.

---

## Problem

Without rules:

```text
No Dependency Injection

No IoC

No Object Management Strategy
```

Spring would not know:

```text
How To Create Objects

How To Connect Objects

How To Manage Dependencies
```

---

## Solution

Spring Core provides the fundamental concepts.

Main concepts:

```text
IoC

Dependency Injection
```

These concepts define how Spring should manage application objects.

---

## What Spring Core Actually Provides

Spring Core defines:

```text
Object Management Rules

Dependency Management Rules

Injection Mechanisms

Container Behaviour
```

It acts as the foundation of Spring.

---

## Internal View

```text
Spring Core
      │
      ▼
Defines IoC
      │
      ▼
Defines DI
      │
      ▼
Provides Rules
      │
      ▼
Other Modules Follow Them
```

---

## Result

Without Spring Core:

```text
No IoC

No DI

No Foundation
```

Spring Core provides the concepts on which everything else is built.

---

# 2. Spring Beans Module

## Situation

Spring Core provides concepts.

But concepts alone cannot create objects.

---

## Problem

Suppose Spring reads:

```xml
<bean id="s1"
      class="Student"/>
```

Questions:

```text
Who Creates Student Object?

Who Stores It?

Who Manages Its Lifecycle?
```

---

## Solution

Spring Beans module handles Bean management.

---

## What It Does

Spring Beans is responsible for:

```text
Bean Creation

Bean Configuration

Bean Storage

Bean Lifecycle Management
```

---

## Example

```xml
<bean id="s1"
      class="Student"/>
```

Spring reads:

```text
Bean Id = s1

Class = Student
```

Then creates:

```java
Student s1 =
        new Student();
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
Dependencies Injected
         │
         ▼
Bean Stored
```

---

## Result

Spring Beans becomes responsible for managing application objects.

---

# Bean Lifecycle View

```text
Bean Definition
        │
        ▼
Bean Creation
        │
        ▼
Dependency Injection
        │
        ▼
Ready To Use
        │
        ▼
Bean Destruction
```

---

# 3. Spring Context Module

## Situation

Beans are created successfully.

Now application code needs them.

Example:

```java
Student s =
        context.getBean("s1");
```

---

## Problem

If beans exist internally:

```text
How Do Developers Access Them?

How Are Bean Relationships Managed?

How Are Resources Loaded?
```

---

## Solution

Spring Context provides an advanced container.

That container is:

```text
ApplicationContext
```

---

## Example

```java
ApplicationContext context =
new ClassPathXmlApplicationContext(
        "beans.xml");
```

Spring loads:

```text
Configuration

Bean Definitions

Dependencies
```

and prepares the container.

---

## Internal Flow

```text
Application Starts
         │
         ▼
ApplicationContext Created
         │
         ▼
Configuration Loaded
         │
         ▼
Beans Created
         │
         ▼
Beans Stored
         │
         ▼
getBean()
         │
         ▼
Bean Returned
```

---

## What Context Module Provides

```text
Bean Access

Resource Loading

Event Handling

Configuration Management

Bean Relationship Management
```

---

## Result

Application code can easily access Spring-managed objects.

---

# How All Three Modules Work Together

Consider:

```xml
<bean id="student"
      class="Student"/>
```

---

## Step 1 : Spring Core

Provides rules:

```text
Use IoC

Use Dependency Injection
```

---

## Step 2 : Spring Beans

Reads bean definition.

Creates object.

```java
Student student =
        new Student();
```

Stores bean.

---

## Step 3 : Spring Context

Provides access.

```java
Student s =
context.getBean(
        "student");
```

Returns bean.

---

# Complete Internal Flow

```text
Application Starts
         │
         ▼
Spring Core Rules Loaded
         │
         ▼
Bean Definitions Read
         │
         ▼
Spring Beans Creates Objects
         │
         ▼
Dependencies Injected
         │
         ▼
Beans Stored
         │
         ▼
ApplicationContext Ready
         │
         ▼
getBean()
         │
         ▼
Bean Returned
```

---

# Relationship Between Modules

```text
Spring Core
      │
      ▼
Provides Concepts
(IoC & DI)

      │
      ▼

Spring Beans
      │
      ▼
Creates And Manages Beans

      │
      ▼

Spring Context
      │
      ▼
Provides Access To Beans
```

---

# Key Observation

Each module has a different responsibility.

```text
Spring Core
=
Rules
```

```text
Spring Beans
=
Creation & Management
```

```text
Spring Context
=
Access & Usage
```

Together:

```text
Spring Core Container
```

manages the complete lifecycle of Spring objects from creation to usage.
