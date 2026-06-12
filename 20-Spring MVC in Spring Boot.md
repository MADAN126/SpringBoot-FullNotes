# Spring MVC in Spring Boot 

---

# 1. Situation (Why Spring MVC Exists)

Imagine building a website without a framework.

A user clicks:

```text
http://localhost:8080/customer
```

You must manually:

- Receive HTTP requests
- Read URLs
- Decide which Java class should execute
- Generate HTML/text response
- Handle errors
- Manage navigation

As the application grows:

```text
10 URLs
↓
50 URLs
↓
100 Controllers
↓
Thousands of requests
```

Managing everything manually becomes difficult.

---

# 2. Problem (Without MVC)

Without a proper architecture:

```text
UI code
+
Business logic
+
Database logic
+
Request handling
=
Mixed together
```

Example:

```java
if(url.equals("/save")){
    // HTML
    // SQL
    // Validation
    // Business logic
}
```

Problems:

- Hard to maintain
- Hard to test
- Difficult to reuse code
- Changes affect unrelated areas

---

# 3. Solution (Spring MVC)

Spring says:

> "Separate responsibilities."

This is called:

```text
MVC
=
Model
+
View
+
Controller
```

Spring MVC provides this structure automatically.

---

# 4. What is Spring MVC?

Spring MVC is Spring's web framework.

It helps build:

- Web Applications
- REST APIs
- Dynamic websites

Built on:

```text
Servlet API
        ↓
Spring MVC
        ↓
Spring Boot Starter Web
```

---

# 5. Why Spring Boot + Spring MVC?

Earlier:

```text
Install Tomcat
↓
Configure web.xml
↓
Deploy WAR
↓
Manual setup
```

Spring Boot:

```text
Add starter dependency
↓
Embedded Tomcat starts
↓
Application runs
```

Much faster.

---

# 6. MVC Architecture

## Mental Picture

```text
User
 ↓
Controller
 ↓
Model
 ↓
Database
 ↓
Controller
 ↓
View
 ↓
User
```

---

# 7. Model

## Situation

Application needs data.

Example:

Customer details.

---

## Responsibility

Model handles:

```text
Business Data
+
Business Logic
```

Example:

```java
Customer
Product
Employee
Order
```

---

## Internal Role

```text
Controller
    ↓
Model fetches data
    ↓
Database
    ↓
Returns data
```

---

## Example

```java
Customer customer;
```

Customer may:

```text
Read DB
Update DB
Process Data
```

---

# 8. View

## Situation

User needs something visible.

Example:

```text
HTML Page
JSON
JSP
Thymeleaf Template
```

---

## Responsibility

View handles:

```text
Presentation Logic
```

It decides:

```text
What user sees
```

NOT:

```text
Business calculations
```

---

## Example

Customer Page:

```text
Textbox
Dropdown
Buttons
Labels
```

---

# 9. Controller

## Situation

Request arrives.

Example:

```text
GET /customer
```

Someone must decide:

```text
What should happen?
```

---

## Responsibility

Controller:

```text
Receives requests
↓
Calls Model
↓
Processes result
↓
Returns View/Response
```

---

## Example

```java
@Controller
public class CustomerController {

}
```

---

# 10. MVC Relationship

```text
View
 ↓ User Action

Controller
 ↓ Business Request

Model
 ↓ Data Access

Database
```

Response:

```text
Database
 ↓

Model
 ↓

Controller
 ↓

View
 ↓

User
```

---

# 11. Spring MVC Internal Workflow

This is the MOST IMPORTANT concept.

---

## High Level Flow

```text
Browser
   ↓
DispatcherServlet
   ↓
Handler Mapping
   ↓
Controller
   ↓
Model
   ↓
View Resolver
   ↓
Response
```

---

# 12. DispatcherServlet (Front Controller)

## Situation

Thousands of URLs exist.

Example:

```text
/hello
/products
/orders
/customers
/login
```

Who receives them?

---

## Solution

Spring uses ONE servlet.

Called:

```text
DispatcherServlet
```

---

## Mental Model

DispatcherServlet says:

> "Give every request to me first.
> I will decide where it should go."

---

## Flow

```text
Browser
    ↓
DispatcherServlet
    ↓
Correct Controller
```

---

# 13. Why Front Controller?

Without it:

```text
URL 1 → Servlet A
URL 2 → Servlet B
URL 3 → Servlet C
```

Every servlet repeats logic.

---

With Front Controller:

```text
All Requests
      ↓
DispatcherServlet
      ↓
Delegates work
```

Benefits:

- Centralized processing
- Error handling
- View resolution
- Localization
- Theme resolution

---

# 14. Request Processing Internally

Suppose user enters:

```text
http://localhost:8899/hello/world
```

---

## Step 1

Browser sends request.

```text
GET /hello/world
```

---

## Step 2

DispatcherServlet intercepts.

```text
DispatcherServlet
↓
Receives request
```

---

## Step 3

HandlerMapping searches.

Question:

```text
Which controller handles /world ?
```

---

## Step 4

Controller found.

Example:

```java
@GetMapping("/world")
```

matched.

---

## Step 5

Controller executes.

```java
return "Hello World";
```

---

## Step 6

DispatcherServlet receives response.

---

## Step 7

Response returned.

```text
Browser
↓
Displays result
```

---

# Complete Internal Flow

```text
Browser
   ↓
DispatcherServlet
   ↓
HandlerMapping
   ↓
Controller
   ↓
Business Logic
   ↓
DispatcherServlet
   ↓
ViewResolver (if needed)
   ↓
HTTP Response
   ↓
Browser
```

---

# 15. Spring Boot Web Application Creation

## Situation

Need a web application quickly.

---

## Steps

### Step 1

Open:

```text
STS
```

---

### Step 2

```text
File
 ↓
New
 ↓
Spring Starter Project
```

---

### Step 3

Fill:

```text
Project Name
Group
Artifact
Package
Java Version
```

---

### Step 4

Add Dependency:

```text
Spring Web
```

Mandatory.

---

## Why?

Spring Web provides:

```text
Spring MVC
+
DispatcherServlet
+
Embedded Tomcat
```

---

# 16. Embedded Server

Earlier:

```text
Application
↓
WAR File
↓
External Tomcat
```

Spring Boot:

```text
Application
↓
Embedded Tomcat
↓
Runs directly
```

---

## Internal Flow

```text
main()
 ↓
SpringApplication.run()
 ↓
Tomcat starts
 ↓
DispatcherServlet registered
 ↓
Application Ready
```

---

# 17. Default Port

By default:

```text
8080
```

Example:

```text
http://localhost:8080
```

---

# 18. Changing Port

application.properties

```properties
server.port=8899
```

---

## Result

Application runs on:

```text
http://localhost:8899
```

---

# 19. Context Path

Situation:

Need application prefix.

Example:

Instead of:

```text
localhost:8899/world
```

Need:

```text
localhost:8899/hello/world
```

---

## Configuration

```properties
server.servlet.context-path=/hello
```

---

## Result

Base URL becomes:

```text
/hello
```

---

# 20. Creating First Endpoint

## Controller

```java
@Controller
public class HelloWorldController {

    @GetMapping("/world")
    @ResponseBody
    public String printHelloWorld() {

        return "Hello world! Welcome to Spring Boot MVC";
    }
}
```

---

# Understanding the Annotations

## @Controller

Situation:

Spring must know:

```text
Which class handles requests?
```

Solution:

```java
@Controller
```

Marks class as:

```text
Web Controller Bean
```

---

## @GetMapping

Situation:

Which URL should execute this method?

Solution:

```java
@GetMapping("/world")
```

Means:

```text
GET /world
↓
Execute method
```

---

## @ResponseBody

Situation:

What should Spring do with return value?

Without it:

```text
return "hello"
```

Means:

```text
Find View named hello
```

---

With it:

```java
@ResponseBody
```

Means:

```text
Write directly to HTTP response body
```

---

# Internal Execution

User enters:

```text
http://localhost:8899/hello/world
```

---

Spring does:

```text
Request arrives
      ↓
DispatcherServlet intercepts
      ↓
HandlerMapping searches
      ↓
HelloWorldController found
      ↓
printHelloWorld()
      ↓
Returns String
      ↓
@ResponseBody detected
      ↓
Write String to HTTP Response
      ↓
Browser displays output
```

---

# Output

```text
Hello world! Welcome to Spring Boot MVC
```

---

# 21. WebApplicationContext

## Situation

Normal ApplicationContext works for Java applications.

Web applications need extra features.

---

## Solution

Spring uses:

```text
WebApplicationContext
```

---

## It Adds

```text
ServletContext access
+
DispatcherServlet support
+
Web infrastructure
```

---

## Relationship

```text
ApplicationContext
        ↓
WebApplicationContext
```

---

# 22. Advantages of Spring MVC

## Separate Roles

```text
Controller → Requests
Model → Data
View → UI
```

No mixing.

---

## Lightweight

Uses:

```text
Servlet Container
```

Efficient.

---

## Powerful Configuration

Easy integration between:

```text
Controllers
Services
Validators
Repositories
```

---

## Rapid Development

Less setup.

More focus on business logic.

---

## Reusable Code

Existing services can be reused.

---

## Easy Testing

Beans are independent.

Can inject mock/test data.

---

## Flexible Mapping

Annotations simplify URL mapping.

Example:

```java
@GetMapping
@PostMapping
@RequestMapping
```

---

# 23. Final Mental Model

When someone types:

```text
http://localhost:8899/hello/world
```

Spring thinks:

```text
User wants something
        ↓
DispatcherServlet catches request
        ↓
Who handles this URL?
        ↓
HandlerMapping finds controller
        ↓
Controller executes logic
        ↓
Return response
        ↓
DispatcherServlet sends HTTP response
        ↓
Browser shows result
```

---

# Final Picture

```text
Browser
    ↓
HTTP Request
    ↓
DispatcherServlet
    ↓
HandlerMapping
    ↓
Controller
    ↓
Model / Services
    ↓
ViewResolver OR @ResponseBody
    ↓
HTTP Response
    ↓
Browser
```

---

# Core Understanding

- Spring MVC is Spring's web framework.
- It follows MVC to separate responsibilities.
- DispatcherServlet is the Front Controller.
- Every request first reaches DispatcherServlet.
- HandlerMapping finds the correct controller.
- Controllers process requests.
- Views render UI or @ResponseBody writes directly to response.
- Spring Boot provides embedded Tomcat.
- Default port is 8080.
- `server.port` changes port.
- `server.servlet.context-path` changes application base path.
- Spring MVC is essentially:

```text
"Receive Request
↓
Find Controller
↓
Execute Logic
↓
Return Response"
```

Once you understand DispatcherServlet, you understand the heart of Spring MVC.
