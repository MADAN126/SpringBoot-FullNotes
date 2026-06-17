# Spring Boot MVC — Understanding the "Why" Behind MVC

---

# Why Did MVC Become Necessary?

## Situation

Early Java web applications were built using Servlets.

One Servlet handled everything:

- Reading requests
- Validating inputs
- Talking to the database
- Applying business rules
- Generating HTML

```java
protected void doGet(...) {

    // Read request
    String id = request.getParameter("id");

    // Database logic
    Customer customer = dao.findById(id);

    // HTML generation
    out.println("<h1>" + customer.getName() + "</h1>");
}
```

---

## Problem

One class becomes responsible for everything.

```
Servlet
 ├─ Request Handling
 ├─ Business Logic
 ├─ Database Logic
 └─ UI Generation
```

As applications grow:

- Difficult to understand
- Difficult to maintain
- Multiple developers modify the same files
- Testing becomes painful
- Reuse becomes almost impossible

---

## Solution

Split responsibilities.

Let each part do only one job.

This separation became:

# MVC

```
Model
View
Controller
```

---

# MVC Mental Model

Think of an online shopping application.

Customer asks:

> "Show me Product #10."

The application behaves like this:

```
Customer
   │
   ▼
Controller
   │
   ▼
Model
   │
   ▼
Database
   │
   ▼
Model
   │
   ▼
Controller
   │
   ▼
View
   │
   ▼
Customer
```

Every component has one responsibility.

---

# MVC Overview

| Component | Responsibility | Think Of It As |
|----------|-------------|------------|
| Model | Business data and rules | Brain |
| Controller | Coordinates everything | Receptionist |
| View | Displays information | Presentation Screen |

---

# Model

---

## Situation

Customer information must be stored.

Example:

```text
Customer Name
Customer Email
Customer Orders
```

Business rules exist:

```text
Balance cannot become negative.
Discount should be calculated.
Orders should update stock.
```

---

## Problem

Where should these rules live?

If placed inside controllers:

```java
@GetMapping(...)
public String process(){

    // database logic

    // discount logic

    // stock logic

    // validation logic

}
```

Controllers become huge.

---

## Solution

Move business knowledge into Model.

Model becomes responsible for:

- Holding application data
- Applying business rules
- Interacting with persistence layers

---

## Code

### Entity

```java
class Customer {

    private Long id;
    private String name;

}
```

### Service

```java
class CustomerService {

    public Customer getCustomer(Long id){

        return repository.findById(id);

    }

}
```

---

## Internal Flow

```
Controller
    │
    ▼
CustomerService
    │
    ▼
Repository
    │
    ▼
Database
```

---

## Output / Result

Business logic stays independent.

Controllers remain simple.

---

## Key Observation

> Model knows HOW the business works.

It does NOT know:

- HTTP requests
- HTML rendering
- URLs

---

# View

---

## Situation

Data must be shown to users.

Example:

```
Customer Name
Balance
Orders
```

Need a UI.

---

## Problem

Should controllers generate HTML?

Example:

```java
out.println("<table>");
out.println("<tr>");
out.println("<td>");
```

Very difficult to maintain.

Design changes become nightmares.

---

## Solution

Create a dedicated layer responsible only for presentation.

That layer is View.

---

## View Responsibility

View decides:

> "How should this information look?"

It does NOT decide:

> "How should this information be obtained?"

---

## Code

Controller:

```java
@GetMapping("/profile")
public String profile(Model model){

    model.addAttribute("name","Madan");

    return "profile";
}
```

View:

```html
<h1 th:text="${name}"></h1>
```

---

## Internal Flow

```
Controller
    │
    ▼
Model Data Added
    │
    ▼
View Receives Data
    │
    ▼
HTML Generated
```

---

## Output / Result

Browser displays:

```text
Madan
```

---

## Key Observation

> View knows presentation, not business logic.

---

# Controller

---

## Situation

Users send requests.

Example:

```text
GET /customer
POST /login
GET /orders
```

Someone must receive these requests.

---

## Problem

Should Models directly receive requests?

No.

Models shouldn't know:

- URLs
- HTTP methods
- Browsers

Views shouldn't receive requests either.

---

## Solution

Introduce a coordinator.

Controller becomes the middleman.

---

## Controller Responsibility

Controller decides:

```text
Which request arrived?
↓
Which business logic should run?
↓
Which response should be returned?
```

---

## Code

```java
@Controller
public class CustomerController {

    @GetMapping("/customer")
    public String customer(){

        return "customer";

    }

}
```

---

## Internal Flow

```
Request
   │
   ▼
Controller
   │
   ▼
Service
   │
   ▼
Database
   │
   ▼
Controller
   │
   ▼
View
```

---

## Output / Result

Correct response reaches the user.

---

## Key Observation

> Controller coordinates.
>
> It should not become the brain of the application.

---

# Spring MVC: What Spring Adds

MVC itself is just an idea.

Spring provides the machinery that makes MVC work automatically.

---

# DispatcherServlet

---

## Situation

Suppose there are 100 controllers.

Requests arrive continuously.

```text
GET /users
GET /products
POST /orders
```

Who decides where each request goes?

---

## Problem

Without a central coordinator:

You would manually configure many servlets.

```
UserServlet
ProductServlet
OrderServlet
PaymentServlet
```

Management becomes difficult.

---

## Solution

Spring introduces DispatcherServlet.

Every request enters through one door.

---

## Internal Flow

```
Browser
   │
   ▼
DispatcherServlet
   │
   ▼
Find Matching Controller
   │
   ▼
Execute Controller Method
   │
   ▼
Prepare Response
   │
   ▼
Browser
```

---

## Key Observation

> DispatcherServlet is the traffic manager of Spring MVC.

You rarely create it manually.

Spring Boot configures it automatically.

---

# Spring MVC Complete Request Flow

```
Browser
   │
   ▼
Embedded Tomcat
   │
   ▼
DispatcherServlet
   │
   ▼
Controller
   │
   ▼
Service Layer
   │
   ▼
Repository
   │
   ▼
Database
   │
   ▼
Repository
   │
   ▼
Service
   │
   ▼
Controller
   │
   ▼
View Resolver / ResponseBody
   │
   ▼
Browser
```

---

# Why Spring MVC Became Popular

| Need | Spring MVC Response |
|---------|----------------|
| Cleaner code | Separation of concerns |
| Large teams | Independent responsibilities |
| Faster development | Auto configuration |
| Testing | Components can be tested individually |
| Reuse | Business logic separated |
| Flexibility | Annotation-based mappings |

---

# Creating a Spring Boot Web Application

---

# Situation

Need a web application quickly.

Without Spring Boot:

- Configure Tomcat manually
- Configure DispatcherServlet manually
- Configure XML files manually

Huge setup effort.

---

# Solution

Spring Boot automates infrastructure.

---

## Steps

### Step 1

Create Spring Starter Project.

```
STS
 ↓
File
 ↓
New
 ↓
Spring Starter Project
```

---

### Step 2

Enter project details.

Example:

```text
Name : SpringMVCDemo
Group : com.example
Artifact : springmvcdemo
```

---

### Step 3

Add dependency.

Mandatory:

```text
Spring Web
```

---

# Why Spring Web Is Mandatory

Spring Web provides:

```
DispatcherServlet
@Controller
@GetMapping
@RequestMapping
@ResponseBody
MVC Infrastructure
```

Without it:

Spring MVC cannot function.

---

# Project Structure

```text
src
├── main
│   ├── java
│   └── resources
│       ├── application.properties
│       ├── static
│       └── templates
│
└── test
```

---

# application.properties

---

# Situation

Spring Boot starts automatically.

Need to know where.

---

## Default Behaviour

Spring Boot uses:

```properties
server.port=8080
```

Meaning:

```text
http://localhost:8080
```

---

# Changing Port

---

## Problem

Port 8080 may already be occupied.

Need another port.

---

## Solution

```properties
server.port=8899
```

---

## Internal Flow

```
Application Starts
      │
      ▼
Spring Reads Properties
      │
      ▼
Embedded Tomcat Configured
      │
      ▼
Tomcat Listens on 8899
```

---

## Result

Application URL becomes:

```text
http://localhost:8899
```

---

# Context Path

---

## Situation

Multiple applications run on one server.

Need separation.

---

## Solution

Use a context path.

```properties
server.servlet.context-path=/hello
```

---

## Internal Flow

```
Spring Reads Context Path
       │
       ▼
Prefix Added To Every URL
```

---

## Result

Before:

```text
localhost:8899/world
```

After:

```text
localhost:8899/hello/world
```

---

## Key Observation

> Context path affects ALL endpoints.

---

# Hello World Controller

```java
@Controller
public class HelloWorldController {

    @GetMapping("/world")
    @ResponseBody
    public String printHelloWorld(){

        return "Hello world! Welcome to Spring Boot MVC";

    }

}
```

---

# @Controller

---

## Situation

Spring scans thousands of classes.

How does it know which classes handle requests?

---

## Solution

Mark them.

```java
@Controller
```

---

## Internal Flow

```
Application Starts
      │
      ▼
Component Scanning Begins
      │
      ▼
@Controller Found
      │
      ▼
Bean Registered
      │
      ▼
Request Mapping Prepared
```

---

## Result

Spring recognizes the class as an MVC controller.

---

## Key Observation

> @Controller makes the class visible to Spring MVC.

---

# @GetMapping

---

## Situation

Different HTTP methods exist.

```text
GET
POST
PUT
DELETE
```

Need to distinguish them.

---

## Solution

Use mapping annotations.

```java
@GetMapping("/world")
```

---

## Internal Flow

```
Spring Startup
      │
      ▼
Scans Controller Methods
      │
      ▼
Finds @GetMapping("/world")
      │
      ▼
Stores Mapping Information
```

---

## Result

Request:

```text
GET /world
```

invokes:

```java
printHelloWorld()
```

---

## Key Observation

> Spring builds an internal routing table during startup.

---

# @ResponseBody

---

## Situation

Controller returns:

```java
return "Hello";
```

What should Spring do?

Two possibilities exist.

---

## Problem

Should it mean:

```text
Render hello.html
```

or

```text
Send Hello to browser
```

Spring needs clarity.

---

## Solution

```java
@ResponseBody
```

---

## Internal Flow

Without:

```
Return "Hello"
      │
      ▼
Treat As View Name
```

With:

```
Return "Hello"
      │
      ▼
Write Directly Into HTTP Response
```

---

## Result

Browser receives:

```text
Hello world! Welcome to Spring Boot MVC
```

---

## Key Observation

> @ResponseBody changes the meaning of the return value.

---

# Complete Execution Flow of Hello World

```
Browser
   │
GET /hello/world
   │
   ▼
Embedded Tomcat
   │
   ▼
DispatcherServlet
   │
   ▼
Routing Table Lookup
   │
   ▼
HelloWorldController
   │
   ▼
printHelloWorld()
   │
   ▼
Returns String
   │
   ▼
@ResponseBody
   │
   ▼
HTTP Response Created
   │
   ▼
Browser Displays Text
```

---

# Final Mental Model

Think of Spring MVC as a restaurant.

```
Customer
   │
   ▼
DispatcherServlet
(Front Desk)
   │
   ▼
Controller
(Waiter)
   │
   ▼
Model / Service
(Chef)
   │
   ▼
Repository
(Storage Room)
   │
   ▼
Database
(Ingredients)
   │
   ▼
View / ResponseBody
(Serving Plate)
   │
   ▼
Customer
```

The power of Spring MVC is not that it introduces three new words.

The power is that it separates responsibilities so each part becomes easier to understand, change, test, and scale while Spring quietly handles the plumbing behind the scenes.
