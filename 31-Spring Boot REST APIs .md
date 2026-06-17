# Spring Boot REST APIs — @RestController, JSON, CRUD, HTTP Methods & @RequestBody

---

# Why Did REST APIs Become Necessary?

---

## Situation

Traditional Spring MVC applications returned web pages.

Example:

```text
Browser
   ↓
Spring MVC
   ↓
JSP / Thymeleaf
   ↓
HTML
```

This worked well when:

- Backend generated UI.
- Browser directly consumed HTML.

---

## Problem

Modern applications changed.

Today, one backend serves many clients:

```text
React Application
Angular Application
Android App
iOS App
Smart TV App
Third-Party Systems
```

Returning HTML became limiting.

Example:

Imagine a mobile app requesting product details.

Would it use:

```html
<h1>iPhone 16</h1>
<p>Price: 90000</p>
```

No.

It needs structured data.

Example:

```json
{
    "name":"iPhone 16",
    "price":90000
}
```

---

## Solution

Return data instead of pages.

This approach became:

# REST APIs

---

# REST Mental Model

Think of REST as:

> "Spring acting as a data provider instead of a page generator."

Traditional MVC:

```text
Request
   ↓
Controller
   ↓
ViewResolver
   ↓
HTML
```

REST MVC:

```text
Request
   ↓
Controller
   ↓
JSON
   ↓
Client
```

---

# @RestController

---

## Situation

Suppose every controller method returns data.

Example:

```java
@GetMapping(...)
@ResponseBody
```

Again:

```java
@GetMapping(...)
@ResponseBody
```

Again:

```java
@GetMapping(...)
@ResponseBody
```

---

## Problem

Repetition.

Every method requires:

```java
@ResponseBody
```

Even though the entire controller returns data.

---

## Solution

Spring introduced:

# @RestController

---

## What Is It Internally?

It combines:

```java
@Controller
+
@ResponseBody
```

into one annotation.

---

## Internal Definition

Conceptually:

```java
@RestController
=
@Controller
+
@ResponseBody
```

---

# Before @RestController

```java
@Controller
public class MacBookController {

    @GetMapping("/mac/details")
    @ResponseBody
    public String getMacBookDetail() {

        return "MAC Book Details : Price 200000. Model 2022";

    }

}
```

---

## Internal Flow

```text
@Controller
      │
@ResponseBody
      │
Method Returns String
      │
Spring Writes To HTTP Response
```

---

# After @RestController

```java
@RestController
public class MacBookController {

    @GetMapping("/mac/details")
    public String getMacBookDetail() {

        return "MAC Book Details : Price 200000. Model 2022";

    }

}
```

---

## Internal Flow

```text
@RestController
       │
@Controller Applied
       │
@ResponseBody Applied
       │
Method Returns String
       │
Response Written Directly
```

---

## Result

Request:

```text
GET /mac/details
```

Response:

```text
MAC Book Details : Price 200000. Model 2022
```

---

## Key Observation

> Use @Controller when returning Views.

> Use @RestController when returning Data.

---

# @Controller vs @RestController

| Feature | @Controller | @RestController |
|----------|-------------|------------------|
| Used For | MVC Views | REST APIs |
| Returns | View Names | Data |
| Needs @ResponseBody | Yes | No |
| ViewResolver Used | Yes | No |
| JSON Responses | Requires @ResponseBody | Automatic |

---

# JSON

---

# Situation

Different applications use different programming languages.

Example:

```text
Java Backend
React Frontend
Android (Kotlin)
iOS (Swift)
Python Service
```

Need a common language.

---

## Problem

Java Objects cannot be directly understood by JavaScript.

Example:

Java:

```java
User user = new User();
```

React cannot understand this.

---

## Solution

Use a language-independent format.

That format became:

# JSON

(JavaScript Object Notation)

---

# Mental Model

Think of JSON as:

> A universally understood package used to transport data.

---

# Example JSON

```json
{
    "name":"John",
    "age":30,
    "car":null
}
```

---

# JSON Syntax Rules

---

## Data Exists As Name-Value Pairs

```json
"name":"John"
```

---

## Multiple Values Use Commas

```json
{
    "name":"John",
    "age":30
}
```

---

## Objects Use Curly Braces

```json
{
    "name":"John"
}
```

---

## Arrays Use Square Brackets

```json
[
    "Java",
    "Spring",
    "React"
]
```

---

# JSON Supported Data Types

| Type | Example |
|--------|---------|
| String | `"Madan"` |
| Number | `25` |
| Boolean | `true` |
| Object | `{}` |
| Array | `[]` |
| Null | `null` |

---

# Why JSON Became Popular

---

## Situation

XML already existed.

---

## Problem

XML is verbose.

XML:

```xml
<firstName>John</firstName>
```

JSON:

```json
"firstName":"John"
```

Much smaller.

---

## Solution

Developers preferred JSON.

---

# JSON vs XML

| Feature | JSON | XML |
|-----------|------|-----|
| Human Readable | Yes | Yes |
| Lightweight | Yes | No |
| Less Verbose | Yes | No |
| Supports Hierarchy | Yes | Yes |
| Language Independent | Yes | Yes |
| Faster Parsing | Generally Yes | Generally Slower |

---

# JSON Example

```json
{
    "employees":[
        {
            "firstName":"John",
            "lastName":"Doe"
        },
        {
            "firstName":"Anna",
            "lastName":"Smith"
        }
    ]
}
```

---

# Equivalent XML

```xml
<employees>

    <employee>
        <firstName>John</firstName>
        <lastName>Doe</lastName>
    </employee>

    <employee>
        <firstName>Anna</firstName>
        <lastName>Smith</lastName>
    </employee>

</employees>
```

---

# CRUD Operations

---

# Situation

Almost every application manipulates data.

Examples:

```text
Register User
View User
Update User
Delete User
```

---

## Problem

Need a standard vocabulary.

---

## Solution

CRUD.

---

# CRUD Meaning

| Operation | Meaning |
|-----------|----------|
| Create | Add New Data |
| Read | Retrieve Data |
| Update | Modify Existing Data |
| Delete | Remove Data |

---

# Database Perspective

| CRUD | SQL |
|--------|------|
| Create | INSERT |
| Read | SELECT |
| Update | UPDATE |
| Delete | DELETE |

---

# HTTP Methods

---

## Situation

REST APIs communicate using HTTP.

Need a way to represent CRUD operations.

---

## Solution

Map CRUD to HTTP methods.

---

# CRUD vs HTTP

| CRUD | HTTP Method |
|--------|-------------|
| Create | POST |
| Read | GET |
| Update | PUT |
| Delete | DELETE |

---

## Mental Model

```text
Database Thinking
       ↓
CRUD Thinking
       ↓
HTTP Thinking
```

Example:

```text
Create User
    ↓
POST /users
```

---

```text
Read User
    ↓
GET /users/1
```

---

```text
Update User
    ↓
PUT /users/1
```

---

```text
Delete User
    ↓
DELETE /users/1
```

---

## Key Observation

> REST APIs become predictable when HTTP methods follow CRUD semantics.

---

# @RequestBody

---

# Situation

Client sends data.

Example:

User Registration.

```text
First Name
Last Name
Email
Mobile
Password
```

Need this data inside Java.

---

## Problem

HTTP request body contains JSON.

Example:

```json
{
    "firstName":"Dilip",
    "lastName":"Singh"
}
```

Java methods expect Java objects.

Example:

```java
UserRegisterRequest request
```

Who converts JSON into Java?

---

## Solution

Spring provides:

# @RequestBody

---

# What Does It Mean?

It tells Spring:

> "Read the HTTP request body and convert it into this Java object."

---

# Request Example

Client Sends:

```json
{
    "firstName":"Dilip",
    "lastName":"Singh",
    "emailId":"dilipsingh1306@gmail.com",
    "mobile":"8125262702",
    "password":"Abc@123"
}
```

---

# Controller

```java
@RestController
public class UserController {

    @PostMapping("/register")
    public String registerUser(
            @RequestBody UserRegisterRequest request) {

        return "User Registered Successfully";

    }

}
```

---

# POJO

```java
public class UserRegisterRequest {

    private String firstName;
    private String lastName;
    private String emailId;
    private String mobile;
    private String password;

    // Getters and Setters

}
```

---

# Internal Flow

```text
Client Sends JSON
       │
       ▼
DispatcherServlet
       │
       ▼
@RequestBody Found
       │
       ▼
HttpMessageConverter Invoked
       │
       ▼
Jackson Reads JSON
       │
       ▼
Java Object Created
       │
       ▼
Controller Method Executes
```

---

# What Is Jackson?

---

## Situation

Spring receives JSON text.

Example:

```json
{
    "firstName":"Dilip"
}
```

Need Java Object.

---

## Problem

Spring itself cannot understand JSON syntax.

---

## Solution

Spring delegates conversion to:

# Jackson

Jackson is a JSON processing library.

---

## Internal Flow

```text
JSON Payload
      │
      ▼
Jackson
      │
      ▼
Setter Methods Called
      │
      ▼
Java Object Ready
```

---

## Example

JSON:

```json
{
    "firstName":"Dilip"
}
```

Jackson performs:

```java
request.setFirstName("Dilip");
```

---

## Result

```java
request.getFirstName()
```

returns:

```text
Dilip
```

---

# Spring Internal Execution of @RequestBody

```text
POST /register
       │
       ▼
DispatcherServlet
       │
       ▼
HandlerMapping
       │
       ▼
UserController.registerUser(...)
       │
       ▼
@RequestBody Detected
       │
       ▼
HttpMessageConverter
       │
       ▼
Jackson Deserialization
       │
       ▼
UserRegisterRequest Created
       │
       ▼
Method Executes
       │
       ▼
Response Returned
```

---

# Media Type

---

## Situation

Spring must know what format the client sends.

Example:

```json
JSON
XML
```

---

## Solution

HTTP uses Content-Type.

Example:

```http
Content-Type: application/json
```

---

## Spring Default Behaviour

Spring Boot REST APIs assume:

```text
application/json
```

as the default media type.

---

## Result

JSON works automatically.

No extra configuration required.

---

# Property Name Matching

---

## Situation

JSON:

```json
{
    "firstName":"Dilip"
}
```

POJO:

```java
private String firstName;
```

Works perfectly.

---

## Problem

JSON:

```json
{
    "first_name":"Dilip"
}
```

POJO:

```java
private String firstName;
```

Names differ.

Jackson cannot match automatically.

---

## Solution

Use:

```java
@JsonProperty("first_name")
private String firstName;
```

---

## Internal Flow

```text
JSON Property
      │
@JsonProperty Mapping
      │
      ▼
Correct Java Field
```

---

## Result

Both formats work.

---

# Final Mental Model

```text
Client
   │
Sends JSON
   │
   ▼
Embedded Tomcat
   │
   ▼
DispatcherServlet
   │
   ▼
HandlerMapping
   │
   ▼
@RestController Method
   │
   ▼
@RequestBody
   │
   ▼
HttpMessageConverter
   │
   ▼
Jackson
(JSON → Java Object)
   │
   ▼
Business Logic Executes
   │
   ▼
Response Returned
   │
   ▼
@ResponseBody Behaviour
(Java Object/String → JSON)
   │
   ▼
Client Receives Data
```

Spring REST APIs are essentially automated translators:

- HTTP requests become Java objects.
- Java objects become JSON responses.
- Developers focus on business logic while Spring and Jackson handle the conversion machinery behind the scenes.
