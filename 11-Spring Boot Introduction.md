# Spring Boot Introduction

---

# Situation

You already learned Spring Core:

- POJO Classes
- Beans
- XML Configuration
- IoC Container
- Dependency Injection
- Bean Wiring

To create even a small Spring application, developers had to:

- Download JAR files
- Configure dependencies
- Create XML files
- Configure container
- Setup project manually

---

# Problem

Most developer time was being spent on:

```text
Project Setup
Configuration
Dependency Management
Server Setup
```

instead of

```text
Business Logic
```

Example:

To create a simple web application:

```text
Create Project
     ↓
Download Spring Jars
     ↓
Configure Build Path
     ↓
Configure Server
     ↓
Configure XML
     ↓
Write Code
```

Too much setup work.

---

# Solution

Spring Boot was introduced.

Goal:

```text
Reduce Configuration
Increase Development Speed
```

Instead of spending hours on setup:

```text
Developer
      ↓
Create Project
      ↓
Write Business Logic
```

Spring Boot handles most configuration automatically.

---

# What is Spring Boot?

Spring Boot is a Spring module that provides:

```text
RAD
=
Rapid Application Development
```

Important:

```text
Spring Boot is NOT a replacement for Spring.

Spring Boot internally uses Spring.
```

Everything learned in Spring:

- IoC
- DI
- Beans
- ApplicationContext
- Bean Wiring

still works inside Spring Boot.

---

# Understanding Spring Boot

Think like this:

Without Spring Boot:

```text
Spring
     +
Manual Configuration
     +
Manual Dependency Setup
     +
Manual Server Setup
```

With Spring Boot:

```text
Spring
     +
Auto Configuration
     +
Automatic Dependency Setup
     +
Embedded Server
```

Result:

```text
Less Configuration
More Development
```

---

# Why Spring Boot Exists

## Situation

Developer wants to create:

```text
Student Management System
```

Developer's focus should be:

```text
Add Student
Update Student
Delete Student
Search Student
```

Not:

```text
Download Jars
Configure XML
Setup Server
Manage Dependencies
```

---

## Problem

Too much infrastructure work.

---

## Solution

Spring Boot automates it.

---

## Result

Developer focuses on:

```text
Business Logic
```

instead of

```text
Configuration
```

---

# Major Advantages of Spring Boot

---

# 1. Rapid Application Development (RAD)

## Situation

Creating Spring projects requires configuration.

---

## Problem

Too much setup before coding.

---

## Solution

Spring Boot provides:

```text
Auto Configuration
```

---

## Internal Flow

```text
Developer Adds Dependency
            │
            ▼
Spring Boot Reads Dependency
            │
            ▼
Automatically Configures Components
            │
            ▼
Application Ready
```

---

## Result

Development becomes faster.

---

# 2. Embedded Web Server

## Situation

Web applications need a server.

Examples:

```text
Tomcat
Jetty
Undertow
```

---

## Traditional Approach

```text
Application Created
        │
        ▼
Install Tomcat
        │
        ▼
Deploy WAR File
        │
        ▼
Run Application
```

---

## Problem

Extra deployment steps.

---

## Solution

Spring Boot embeds server inside application.

---

## Internal Flow

```text
Application Starts
        │
        ▼
Embedded Tomcat Starts
        │
        ▼
Application Ready
```

---

## Result

Run application directly.

No separate server installation required.

---

# 3. Starter Dependencies

## Situation

A Web Application needs:

```text
Spring Core
Spring MVC
Jackson
Validation
Tomcat
Logging
```

---

## Traditional Spring

Developer manually finds every dependency.

```text
Dependency 1
Dependency 2
Dependency 3
Dependency 4
...
```

---

## Problem

Dependency management becomes difficult.

---

## Solution

Spring Boot Starters.

Example:

```xml
spring-boot-starter-web
```

---

## What Happens Internally

Developer adds:

```xml
spring-boot-starter-web
```

Spring Boot automatically pulls:

```text
Spring MVC
Tomcat
Jackson
Validation
Logging
```

---

## Result

One dependency brings everything required.

---

# 4. Auto Configuration

## Situation

Application requires:

```text
Dispatcher Servlet
Tomcat
Database Configuration
Bean Configuration
```

---

## Problem

Developer manually configures everything.

---

## Solution

Spring Boot analyzes project dependencies.

---

## Internal Flow

```text
Application Starts
        │
        ▼
Spring Boot Scans Classpath
        │
        ▼
Checks Dependencies
        │
        ▼
Creates Required Configuration
        │
        ▼
Application Ready
```

---

## Result

Very little configuration required.

---

# 5. Configuration Properties

## Situation

Application values change.

Examples:

```text
Database URL
Username
Password
Port Number
```

---

## Problem

Hardcoding values inside Java classes.

```java
String url =
"jdbc:mysql://localhost";
```

---

## Solution

Keep configuration outside code.

Example:

```properties
server.port=8080
```

---

## Internal Flow

```text
Properties File
        │
        ▼
Spring Boot Reads Values
        │
        ▼
Injects Configuration
        │
        ▼
Application Uses Values
```

---

## Result

Configuration can change without modifying code.

---

# 6. Actuator

## Situation

Application is running in production.

Need information like:

```text
Health
Memory Usage
Metrics
Application Status
```

---

## Problem

No easy way to monitor application.

---

## Solution

Spring Boot Actuator.

---

## Internal Flow

```text
Application Running
        │
        ▼
Actuator Collects Information
        │
        ▼
Endpoints Exposed
        │
        ▼
Monitoring Available
```

---

## Result

Easy monitoring and troubleshooting.

---

# Creating Spring Boot Project

Two common approaches.

---

# Method 1 : Spring Initializr

Website:

```text
https://start.spring.io
```

---

## Flow

```text
Open start.spring.io
        │
        ▼
Select Maven
        │
        ▼
Select Java
        │
        ▼
Select Spring Boot Version
        │
        ▼
Choose Dependencies
        │
        ▼
Generate Project
        │
        ▼
Download ZIP
        │
        ▼
Import Into IDE
```

---

## Result

Project is ready instantly.

---

# Method 2 : STS

STS:

```text
Spring Tool Suite
```

Eclipse-based IDE specially designed for Spring development.

---

## Flow

```text
Open STS
       │
       ▼
File
       │
       ▼
New
       │
       ▼
Spring Starter Project
       │
       ▼
Select Dependencies
       │
       ▼
Finish
```

---

## Result

Project automatically created and imported.

---

# Biggest Observation

In Spring Framework:

```text
Find Jars
Download Jars
Add Jars
Manage Jars
```

Developer does everything.

---

In Spring Boot:

```text
Add Dependency
```

Spring Boot does the rest.

---

# Spring Boot and Spring Core

Many beginners think:

```text
Spring Boot = Different Framework
```

Wrong understanding.

---

Actual Flow:

```text
Spring Boot
      │
      ▼
Uses Spring Core
      │
      ▼
Uses IoC
      │
      ▼
Uses DI
      │
      ▼
Uses Beans
```

Everything learned in Spring Core still applies.

---

# XML Configuration Still Works

Spring Boot can still use:

```xml
<bean id="student"
      class="com.dilip.beans.Student">

    <property
        name="name"
        value="Dilip Singh"/>

</bean>
```

Spring Boot reads XML exactly like Spring Framework.

---

# Internal Execution

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
Student Bean Created
        │
        ▼
Property Values Injected
        │
        ▼
Bean Stored In Container
        │
        ▼
getBean("student")
        │
        ▼
Student Object Returned
```

---

# Key Observation

Nothing changes in:

```text
IoC
DI
Bean Creation
Bean Wiring
ApplicationContext
XML Configuration
```

Spring Boot simply removes project setup and configuration burden.

---

# Final Understanding

```text
Spring Framework
=
Provides Features
(IoC, DI, Beans, Context)

            +

Spring Boot
=
Provides Faster Setup
Auto Configuration
Embedded Server
Starter Dependencies
Production Support
```

Final Result:

```text
Same Spring Concepts
+
Less Configuration
+
Faster Development
=
Spring Boot
```
