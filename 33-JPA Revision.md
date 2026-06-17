# Spring Boot Application Startup, Service Layer & Repository Layer

---

# The Bigger Picture

Until now, we have seen individual REST pieces:

```text
@RestController
@RequestBody
Jackson
JSON
HTTP Methods
```

But one important question still remains:

> How does Spring Boot know what to create?
>
> How are all these pieces connected together?

This chapter explains the architecture Spring Boot builds behind the scenes.

---

# Complete Application Architecture

```text
Client
   │
HTTP Request
   ▼
Controller Layer
   │
Delegates Work
   ▼
Service Layer
   │
Business Logic
   ▼
Repository Layer
   │
Database Operations
   ▼
Database
```

Response travels back in reverse.

```text
Database
   ▼
Repository
   ▼
Service
   ▼
Controller
   ▼
JSON Response
   ▼
Client
```

---

# Why Spring Boot Exists

---

## Situation

Before Spring Boot, setting up Spring applications was painful.

Developers had to configure:

```text
DispatcherServlet
Tomcat
Component Scanning
Beans
View Resolvers
Data Sources
Jackson
XML Configurations
```

Even a simple application required huge setup.

---

## Problem

Too much boilerplate.

Example:

```text
I want to create one endpoint.
```

But before that:

```text
100+ lines of configuration.
```

---

## Solution

Spring Boot introduced:

# @SpringBootApplication

One annotation.

Many configurations.

---

# @SpringBootApplication

---

## Mental Model

Think of it as:

> "Turn on Spring Boot."

When Spring sees this annotation:

```java
@SpringBootApplication
public class Application {
}
```

it says:

```text
Okay.
Let me prepare everything needed
to run this application.
```

---

# Internal Structure

Spring Boot documentation says:

```java
@SpringBootApplication
=
@Configuration
+
@EnableAutoConfiguration
+
@ComponentScan
```

---

# Internal Flow

```text
@SpringBootApplication
        │
        ▼
@Configuration
        │
@EnableAutoConfiguration
        │
@ComponentScan
        │
        ▼
Spring Application Ready
```

---

# @Configuration

---

## Situation

Sometimes Spring cannot discover everything automatically.

You may want to manually create objects.

Example:

```text
Third-party classes
Utility objects
Custom beans
```

---

## Problem

How do we tell Spring:

> "Please manage this object too."

---

## Solution

Use:

# @Configuration

---

## Mental Model

Think of it as:

> "This class teaches Spring how to create beans."

---

# Example

```java
@Configuration
public class AppConfig {

    @Bean
    public Formatter formatter() {

        return new Formatter();

    }

}
```

---

# Internal Flow

```text
Spring Starts
      │
@Configuration Found
      │
@Bean Methods Executed
      │
Objects Created
      │
Beans Registered
```

---

# Result

```java
Formatter
```

becomes a Spring Bean.

---

# Key Observation

> @Configuration allows manual bean creation.

---

# @EnableAutoConfiguration

---

## Situation

You added:

```xml
spring-boot-starter-web
```

in pom.xml.

---

## Problem

Should you manually configure?

```text
DispatcherServlet?
Tomcat?
Jackson?
MessageConverters?
```

That would defeat Spring Boot's purpose.

---

## Solution

Spring Boot automatically configures them.

Using:

# @EnableAutoConfiguration

---

# Mental Model

Think of it as:

> "I noticed what libraries you added.
>
> Let me configure them for you."

---

# Internal Flow

Suppose dependency added:

```xml
spring-boot-starter-web
```

Spring thinks:

```text
Web Starter Present?
      │
      ▼
Configure Tomcat
Configure DispatcherServlet
Configure Jackson
Configure MVC Infrastructure
```

---

# Another Example

Dependency:

```xml
spring-boot-starter-data-jpa
```

Spring thinks:

```text
JPA Present?
      │
      ▼
Configure EntityManager
Configure Hibernate
Configure Repositories
```

---

# Result

Less configuration.

Faster development.

---

# Key Observation

> Auto-configuration is based on what exists in the classpath.

---

# @ComponentScan

---

## Situation

You created:

```java
@RestController
@Service
@Repository
@Component
```

classes.

---

## Problem

How does Spring discover them?

You never explicitly registered them.

---

## Solution

Use:

# @ComponentScan

---

# Mental Model

Think of it as:

> "Walk through packages and find Spring-managed classes."

---

# Example Structure

```text
com.example

├── Application.java
├── controller
├── service
├── repository
└── model
```

---

# Internal Flow

```text
Spring Starts
      │
@ComponentScan Begins
      │
Current Package
      │
Sub-Packages
      │
Annotations Found
      │
Beans Registered
```

---

# Detects

```text
@Controller
@RestController
@Service
@Repository
@Component
```

---

# Key Observation

> The package containing the main class becomes the root package.

---

# SpringApplication Class

---

## Situation

You write:

```java
public static void main(String[] args) {

}
```

How does the application actually start?

---

## Solution

Spring provides:

# SpringApplication

---

# Code

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {

        SpringApplication.run(
                Application.class,
                args);

    }

}
```

---

# What Happens Internally?

---

## Internal Flow

```text
main()
   │
   ▼
SpringApplication.run()
   │
   ▼
ApplicationContext Created
   │
   ▼
Component Scan Starts
   │
   ▼
Auto Configuration Executes
   │
   ▼
Beans Created
   │
   ▼
Embedded Tomcat Starts
   │
   ▼
DispatcherServlet Registered
   │
   ▼
Application Ready
```

---

# Result

Application becomes available.

Example:

```text
http://localhost:8080
```

---

# Key Observation

> SpringApplication.run() is the actual bootstrap process.

---

# Why Layers Exist

---

## Situation

Suppose a controller performs everything.

Example:

```java
Save Data
Validate Data
Calculate Fees
Send Email
Access Database
Return Response
```

---

## Problem

One class becomes enormous.

Example:

```text
1000+ lines.
```

Difficult to:

```text
Maintain
Test
Reuse
Debug
Extend
```

---

## Solution

Separate responsibilities.

Using layers.

---

# Layered Architecture

```text
Controller Layer
        │
        ▼
Service Layer
        │
        ▼
Repository Layer
        │
        ▼
Database
```

---

# Controller Layer

---

## Responsibility

Handles:

```text
HTTP Requests
JSON Conversion
HTTP Responses
```

---

## Should NOT Handle

```text
Business Rules
Complex Calculations
Database Queries
```

---

# Service Layer

---

# Situation

Business rules are needed.

Examples:

```text
Calculate Discounts
Validate Admission Eligibility
Send Notifications
Approve Loans
```

---

## Problem

Controllers shouldn't contain business logic.

---

## Solution

Create:

# Service Layer

---

# Mental Model

Think of Service Layer as:

> "The brain of the application."

---

# Responsibilities

```text
Business Logic
Validation
Workflow Coordination
Transaction Management
Calling Multiple Repositories
```

---

# Annotation

```java
@Service
```

---

# Internal Flow

```text
@ComponentScan
      │
@Service Found
      │
Bean Created
      │
Available For Injection
```

---

# Controller Example

```java
@RestController
@RequestMapping("/admission")
public class UniversityAdmissionsController {

    @Autowired
    UniversityAdmissionsService service;

}
```

---

# What Is @Autowired Doing?

---

## Situation

Controller needs Service.

---

## Problem

Should we manually create?

```java
new UniversityAdmissionsService();
```

No.

Spring should manage it.

---

## Solution

Dependency Injection.

Using:

```java
@Autowired
```

---

# Internal Flow

```text
Controller Bean Created
        │
@Autowired Found
        │
Search Container
        │
@Service Bean Located
        │
Injected Automatically
```

---

# Result

Controller receives:

```java
UniversityAdmissionsService
```

without creating it manually.

---

# Admission POJO

```java
public class Admission {

    private String name;
    private double cgpa;
    private long contact;

    // getters and setters

}
```

---

# Creating Admission

---

## Controller

```java
@PostMapping("/create")
public String createAdmission(
        @RequestBody Admission admission) {

    return service.createAdmission(admission);

}
```

---

# Flow

```text
JSON Request
      │
@RequestBody
      │
Admission Object
      │
Controller
      │
Service
```

---

# Service

```java
@Service
public class UniversityAdmissionsService {

    public String createAdmission(
            Admission admission) {

        boolean isCreated = false;

        String result;

        if(isCreated) {

            result =
              "Admission Created Successfully";

        }
        else {

            result =
              "Admission Not Created Successfully";

        }

        return result;
    }

}
```

---

# Internal Flow

```text
Controller
     │
Passes Admission
     │
Service Executes Logic
     │
Repository Called
(Optional)
     │
Result Generated
     │
Controller Returns Response
```

---

# Fetching Admission

---

## Controller

```java
@GetMapping("/1123353")
public Admission getAdmissionDetails() {

    return service.getAdmissionDetails(
            "1123353");

}
```

---

## Service

```java
public Admission getAdmissionDetails(
        String admissionId) {

    Admission ad = new Admission();

    ad.setCgpa(88.99);
    ad.setContact(88888);
    ad.setName("Dilip Singh");

    return ad;
}
```

---

# Flow

```text
GET Request
      │
Controller
      │
Service
      │
Admission Returned
      │
Jackson
      │
JSON Response
```

---

# Repository Layer

---

# Situation

Eventually data must be stored.

Examples:

```text
MySQL
PostgreSQL
Oracle
MongoDB
```

---

## Problem

Service Layer shouldn't know SQL details.

Example:

Service shouldn't contain:

```sql
SELECT *
FROM admissions;
```

---

## Solution

Create:

# Repository Layer

---

# Mental Model

Think of Repository Layer as:

> "The gateway to the database."

---

# Responsibilities

```text
Save Data
Read Data
Update Data
Delete Data
Database Queries
```

---

# Architecture Flow

```text
Client
   │
Controller
   │
Service
   │
Repository
   │
Database
```

---

# Why This Separation Matters

| Layer | Responsibility |
|---------|---------------|
| Controller | HTTP Communication |
| Service | Business Rules |
| Repository | Database Access |
| Database | Data Storage |

---

# Complete Internal Flow

```text
Application Starts
       │
@SpringBootApplication
       │
SpringApplication.run()
       │
ApplicationContext Created
       │
@ComponentScan
       │
Beans Registered
       │
Tomcat Starts
       │
Application Ready
       │
────────────────────
       │
Client Sends Request
       │
DispatcherServlet
       │
Controller
       │
@Service
(Business Logic)
       │
@Repository
(Database Access)
       │
Database
       │
Repository
       │
Service
       │
Controller
       │
Jackson
       │
JSON Response
       │
Client
```

---

# Final Mental Model

Spring Boot is essentially an automation engine built around layered architecture.

```text
@SpringBootApplication
        │
Starts Everything
        │
Creates Beans
        │
Connects Dependencies
        │
Runs Tomcat
        │
Accepts Requests
        │
Controller
        │
Service
        │
Repository
        │
Database
        │
Response Returned
```

The real power of Spring Boot is not just that it reduces configuration.

It gives structure.

- Controllers focus only on HTTP.
- Services focus only on business decisions.
- Repositories focus only on persistence.
- Spring focuses on wiring everything together automatically.

As applications grow from 5 endpoints to 500 endpoints, this separation is what keeps the system understandable and maintainable.
