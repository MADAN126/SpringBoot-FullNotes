# Spring Framework Introduction

## The Problem

Consider a Banking Application.

Requirements:

```text
Create Account

Transfer Money

Generate Statement

Check Balance
```

These are the actual business features.

---

But building the application requires much more than business logic.

Developers also need to handle:

```text
Object Creation

Object Communication

Database Access

Transactions

Security

Logging

Exception Handling
```

As applications grow, managing these concerns manually becomes difficult.

---

## Why Spring Was Created

Developers should spend most of their time solving business problems.

Example:

```text
Bank Employee Cares About

Account Creation

Money Transfer

Statement Generation
```

Not:

```text
How Objects Are Created

How Dependencies Are Connected

How Transactions Are Managed
```

Spring was created to handle these common infrastructure problems so developers can focus on business requirements.

---

# What Spring Actually Does

Spring sits between your application components and manages many repetitive tasks.

```text
Application Components
           │
           ▼
      Spring
           │
           ▼
Infrastructure Support
```

Instead of manually managing everything, developers let Spring manage it.

---

## Example

Without Spring:

```java
Address address =
        new Address();

Customer customer =
        new Customer();

customer.setAddress(
        address);
```

Developer creates and connects objects manually.

---

With Spring:

```text
Spring Creates Objects

Spring Connects Objects

Spring Manages Lifecycle
```

Developer focuses on application logic.

---

# Loose Coupling

Consider:

```text
Customer
      │
      ▼
Address
```

Customer depends on Address.

If Customer creates Address directly:

```java
Address address =
        new Address();
```

Customer becomes tightly connected to Address.

Changing Address later becomes harder.

---

Instead:

```text
Spring Creates Address
         │
         ▼
Spring Supplies Address
         │
         ▼
Customer Uses Address
```

Customer only uses the dependency.

Spring manages the dependency.

This approach is called:

```text
Loose Coupling
```

---

## Why Loose Coupling Matters

```text
Easier Maintenance

Easier Testing

Easier Modification

Better Scalability
```

Applications become easier to change without affecting multiple components.

---

# Dependency Injection (DI)

Imagine:

```text
Customer Needs Address
```

Without Spring:

```java
Address address =
        new Address();

Customer customer =
        new Customer();

customer.setAddress(
        address);
```

Developer manually supplies the dependency.

---

With Spring:

```text
Spring Creates Address
         │
         ▼
Spring Injects Address
         │
         ▼
Customer Ready
```

The process of supplying required objects is called:

```text
Dependency Injection
```

---

## Internal Flow

```text
Bean Created
      │
      ▼
Dependency Created
      │
      ▼
Dependency Injected
      │
      ▼
Object Ready
```

---

# Inversion of Control (IoC)

Normally:

```text
Developer Creates Objects
```

Example:

```java
Customer customer =
        new Customer();
```

Developer controls object creation.

---

With Spring:

```text
Spring Creates Objects
```

Example:

```text
Container Starts
        │
        ▼
Spring Creates Beans
        │
        ▼
Spring Stores Beans
        │
        ▼
Application Uses Beans
```

Control moves from developer to Spring.

This is called:

```text
Inversion of Control
```

---

## Relationship Between IoC and DI

```text
IoC
 │
 ▼
Spring Controls Objects
```

```text
DI
 │
 ▼
Spring Supplies Dependencies
```

Dependency Injection is one way Spring achieves IoC.

---

# Aspect-Oriented Programming (AOP)

Many features are needed in multiple places.

Example:

```text
Logging

Security

Transactions
```

Suppose:

```text
Transfer Money

Create Account

Close Account
```

All three operations need logging.

Without AOP:

```text
Logging Code

Business Code

Logging Code

Business Code

Logging Code

Business Code
```

Repeated everywhere.

---

With AOP:

```text
Business Logic
      │
      ▼
Spring Adds Logging
Automatically
```

Business code remains clean.

---

## Internal Flow

```text
Request
      │
      ▼
Security Check
      │
      ▼
Logging
      │
      ▼
Business Logic
      │
      ▼
Response
```

Spring can apply these extra steps automatically.

---

# Spring MVC

Web applications receive requests from users.

Example:

```text
/login

/register

/transfer

/accounts
```

Someone must decide:

```text
Which Code Handles
Which Request
```

Spring MVC manages this process.

---

## Request Flow

```text
User Request
      │
      ▼
Spring MVC
      │
      ▼
Controller
      │
      ▼
Business Logic
      │
      ▼
Response
```

This makes web application development easier.

---

# Integration Support

Applications rarely work alone.

They often communicate with:

```text
Databases

Messaging Systems

ORM Frameworks

External Services
```

Spring provides easy integration with technologies such as:

```text
Hibernate

JPA

JMS
```

---

## Example Flow

```text
Application
      │
      ▼
Spring
      │
      ▼
Hibernate
      │
      ▼
Database
```

Spring acts as the bridge between different technologies.

---

# Spring Ecosystem

As application requirements increased, Spring expanded into multiple projects.

```text
Spring Framework
        │
        ├── Spring Boot
        │
        ├── Spring Security
        │
        ├── Spring Data
        │
        ├── Spring Cloud
        │
        └── Spring Batch
```

Each project focuses on solving a specific problem.

---

## Spring Boot

```text
Simplifies Project Setup

Reduces Configuration

Faster Development
```

---

## Spring Security

```text
Authentication

Authorization

Application Security
```

---

## Spring Data

```text
Database Operations

Repository Support

Data Access Simplification
```

---

## Spring Cloud

```text
Microservices

Distributed Systems

Cloud Applications
```

---

## Spring Batch

```text
Large Data Processing

Scheduled Jobs

Bulk Operations
```

---

# Complete Picture

```text
Business Requirement
         │
         ▼
Application Code
         │
         ▼
Spring Framework
         │
         ├── Object Management
         ├── Dependency Management
         ├── Security
         ├── Transactions
         ├── Integration
         └── Web Support
         │
         ▼
Application Ready
```

---

# Key Observation

Developers should focus on:

```text
Business Logic
```

Spring focuses on:

```text
Object Management

Dependency Management

Security

Transactions

Integration
```

This separation is the main reason Spring became one of the most widely used Java frameworks.
