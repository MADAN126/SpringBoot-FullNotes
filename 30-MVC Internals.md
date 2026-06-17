# Spring Boot MVC Internals — Request Processing, Handler Mapping & Request Mapping

---

# The Bigger Picture

Until now we understood:

```
Browser
   ↓
Controller
   ↓
Response
```

But Spring does a lot more internally.

The real flow is:

```
Browser
   ↓
Tomcat
   ↓
DispatcherServlet
   ↓
HandlerMapping
   ↓
Controller Method
   ↓
Business Logic
   ↓
View Resolver / ResponseBody
   ↓
Browser
```

This chapter focuses on understanding what happens between:

> "I hit Enter in the browser"

and

> "I receive a response."

---

# WebApplicationContext

---

## Situation

Spring needs a place to store and manage all objects used by the web application.

Examples:

```text
Controllers
Services
Repositories
HandlerMappings
ViewResolvers
DispatcherServlet Dependencies
```

---

## Problem

A normal Java application uses:

```text
ApplicationContext
```

But web applications need additional information.

Example:

```text
ServletContext
HTTP Environment
DispatcherServlet
Web-specific Beans
```

A normal ApplicationContext doesn't understand these.

---

## Solution

Spring creates:

# WebApplicationContext

It is simply:

```
ApplicationContext
        +
Web-specific capabilities
```

---

## Internal Flow

```
Application Starts
       │
       ▼
Spring Creates
ApplicationContext
       │
       ▼
Spring Extends It Into
WebApplicationContext
       │
       ▼
Web Beans Are Registered
```

---

## What It Stores

```
WebApplicationContext
├─ Controllers
├─ Services
├─ Repositories
├─ DispatcherServlet
├─ HandlerMappings
├─ ViewResolvers
└─ Other Spring Beans
```

---

## Key Observation

> WebApplicationContext is Spring's web-aware container.

Without it:

Spring MVC cannot function.

---

# DispatcherServlet

---

## Situation

Imagine 500 URLs.

```text
/users
/orders
/products
/login
/logout
```

Requests arrive continuously.

Someone must decide:

> Which controller should handle this request?

---

## Problem

Without a central entry point:

```
UserServlet
OrderServlet
ProductServlet
LoginServlet
```

You manually manage everything.

Very difficult.

---

## Solution

Spring creates one Front Controller:

# DispatcherServlet

Every request enters through it.

---

## Internal Flow

```
Browser
   │
HTTP Request
   │
   ▼
DispatcherServlet
```

DispatcherServlet then delegates work.

---

## DispatcherServlet Responsibility

It does NOT execute business logic.

It only coordinates.

It asks:

```text
Who should handle this request?
Where should response go?
Should a View be rendered?
```

---

## Flow Diagram

```
Browser
   │
   ▼
DispatcherServlet
   │
   ├─────────────► HandlerMapping
   │
   ├─────────────► Controller
   │
   ├─────────────► ViewResolver
   │
   ▼
Response
```

---

## Key Observation

> DispatcherServlet is the traffic police of Spring MVC.

It knows where to send requests.

It does not solve the request itself.

---

# HandlerMapping

---

## Situation

DispatcherServlet receives:

```text
GET /message
```

It knows a request arrived.

But it doesn't know:

```text
Which controller?
Which method?
```

---

## Problem

How can Spring locate the correct method among hundreds?

Example:

```java
@GetMapping("/message")

@GetMapping("/cost")

@GetMapping("/ipad/cost")
```

Need a lookup mechanism.

---

## Solution

Spring creates:

# HandlerMapping

HandlerMapping acts like a routing table.

---

## Mental Model

Think of it as:

> Google Maps for URLs.

You provide:

```text
GET /message
```

HandlerMapping says:

```text
Go to:
IphoneController.printIphoneMessage()
```

---

## Internal Flow

Application Startup:

```
Spring Scans Controllers
        │
        ▼
Finds URL Mappings
        │
        ▼
Builds Routing Table
        │
        ▼
Stores Inside HandlerMapping
```

---

## Example

Controller:

```java
@Controller
public class IphoneController {

    @GetMapping("/message")
    public String printMessage(){}

    @GetMapping("/cost")
    public String printCost(){}

}
```

Spring internally builds:

| Request | Handler |
|----------|----------|
| GET /message | printMessage() |
| GET /cost | printCost() |

---

## Request Time Flow

```
GET /message
      │
      ▼
DispatcherServlet
      │
      ▼
HandlerMapping Lookup
      │
      ▼
printMessage()
```

---

## Key Observation

> HandlerMapping is created during startup, not during every request.

This makes request processing fast.

---

# Multiple Controllers

---

## Situation

Large applications contain many controllers.

Example:

```java
@Controller
class IphoneController
```

```java
@Controller
class IpadController
```

---

## Code

### IphoneController

```java
@Controller
public class IphoneController {

    @GetMapping("/message")
    @ResponseBody
    public String printIphoneMessage() {

        return "Welcome to Iphone World.";

    }

    @GetMapping("/cost")
    @ResponseBody
    public String printIphone14Cost() {

        return "Price is INR : 150000";

    }

}
```

---

### IpadController

```java
@Controller
public class IpadController {

    @GetMapping("/ipad/cost")
    @ResponseBody
    public String printIPadCost() {

        return "Ipad Price is INR : 200000";

    }

}
```

---

## Startup Internal Flow

```
Spring Starts
      │
      ▼
Component Scan Begins
      │
      ▼
@Controller Found
      │
      ▼
HandlerMappings Created
```

---

## Result

Spring builds:

| URL | Method |
|-------|--------|
| /message | printIphoneMessage() |
| /cost | printIphone14Cost() |
| /ipad/cost | printIPadCost() |

---

# Complete Request Execution

Suppose request is:

```text
http://localhost:6655/apple/message
```

---

## Internal Flow

```
Browser
   │
GET /apple/message
   │
   ▼
Tomcat
   │
   ▼
DispatcherServlet
   │
   ▼
HandlerMapping
   │
   ▼
IphoneController
   │
   ▼
printIphoneMessage()
   │
   ▼
Service Layer
(Optional)
   │
   ▼
Return Result
   │
   ▼
@ResponseBody
   │
   ▼
HTTP Response
   │
   ▼
Browser
```

---

## Result

Browser displays:

```text
Welcome to Iphone World.
```

---

# ViewResolver

---

## Situation

Controller finishes execution.

Now Spring has the result.

Question:

> Should Spring return a View?

or

> Should Spring return raw data?

---

## Problem

Need a mechanism to locate views.

Example:

```text
home.jsp
profile.jsp
login.jsp
```

---

## Solution

Spring uses:

# ViewResolver

---

## Internal Flow

```
Controller Returns
"home"
      │
      ▼
ViewResolver
      │
      ▼
Finds home.jsp
      │
      ▼
Renders JSP
```

---

## Result

Browser receives HTML.

---

## Key Observation

ViewResolver is only useful when Views exist.

---

# REST APIs and Why ViewResolver Disappears

---

## Situation

Modern applications often separate frontend and backend.

Example:

Frontend:

```text
Angular
React
Mobile Apps
```

Backend:

```text
Spring Boot REST APIs
```

---

## Problem

Backend no longer returns JSP pages.

It returns:

```json
{
    "name":"Madan"
}
```

ViewResolver becomes unnecessary.

---

## Solution

Return data directly.

Use:

```java
@ResponseBody
```

or

```java
@RestController
```

---

## Internal Flow

```
Controller
    │
    ▼
Object Returned
    │
    ▼
JSON Conversion
    │
    ▼
HTTP Response
```

---

## Result

Frontend teams consume APIs independently.

---

## Key Observation

> Microservices communicate through REST, not JSP pages.

That's why REST dominates modern Spring development.

---

# @Controller

---

## Situation

Spring scans thousands of classes.

How does it know which are controllers?

---

## Solution

Mark them.

```java
@Controller
```

---

## Internal Flow

```
Component Scan
      │
      ▼
@Controller Found
      │
      ▼
Bean Registered
      │
      ▼
Eligible For Request Mapping
```

---

## Result

Spring manages the controller.

---

# @ResponseBody

---

## Situation

Method returns:

```java
return "Welcome";
```

What does Spring do?

---

## Problem

Two possibilities exist.

### Interpretation 1

```text
View Name = Welcome
```

### Interpretation 2

```text
Response Body = Welcome
```

---

## Solution

Use:

```java
@ResponseBody
```

---

## Internal Flow

Without:

```
Return String
    │
    ▼
ViewResolver Invoked
```

With:

```
Return String
    │
    ▼
Written Directly To HttpResponse
```

---

## Result

Browser displays:

```text
Welcome
```

---

## Key Observation

> @ResponseBody changes the meaning of the return value.

---

# @RequestMapping

---

## Situation

Need to connect URLs with methods.

Example:

```text
/message
/login
/orders
```

---

## Solution

Use:

```java
@RequestMapping
```

It tells Spring:

> "When this request arrives, execute this method."

---

## Simplest Form

```java
@RequestMapping("/message")
```

---

## Internal Flow

Startup:

```
Spring Finds
@RequestMapping
       │
       ▼
Creates URL Mapping
       │
       ▼
Stores In HandlerMapping
```

---

## Result

Any request to:

```text
/message
```

invokes the method.

---

# Important Behaviour

```java
@RequestMapping("/message")
```

means:

```java
@RequestMapping(value="/message")
```

or

```java
@RequestMapping(path="/message")
```

All are equivalent.

---

# HTTP Method Restriction

---

## Situation

Sometimes only GET should work.

---

## Solution

```java
@RequestMapping(
    value="/message",
    method=RequestMethod.GET
)
```

---

## Internal Flow

```
Incoming Request
      │
      ▼
GET ?
      │
 ┌────┴─────┐
 │          │
YES         NO
 │          │
 ▼          ▼
Execute   405 Error
Method
```

---

## Result

Only GET requests succeed.

---

# 405 Method Not Allowed

---

## Situation

Controller:

```java
@RequestMapping(
    value="/message",
    method=RequestMethod.GET
)
```

Request:

```text
POST /message
```

---

## Result

Spring returns:

```json
{
    "status":405,
    "error":"Method Not Allowed"
}
```

---

## Why?

Because HandlerMapping found the URL.

But HTTP method didn't match.

---

# Multiple HTTP Methods

---

## Code

```java
@RequestMapping(
    value="/message",
    method={
        RequestMethod.GET,
        RequestMethod.POST
    }
)
```

---

## Result

Supports:

```text
GET /message
POST /message
```

---

## Internal Flow

```
Request
   │
   ▼
GET or POST?
   │
   ▼
Execute Method
```

---

# Multiple URLs

---

## Code

```java
@RequestMapping(
    value={"/message","/msg/iphone"},
    method={
        RequestMethod.GET,
        RequestMethod.POST
    }
)
```

---

## Result

All work:

```text
GET /message
POST /message
GET /msg/iphone
POST /msg/iphone
```

---

# Same URI, Different HTTP Methods

---

## Code

```java
@RequestMapping(
    value="/mac",
    method=RequestMethod.GET
)
public String getMac(){

    return "MAC";

}
```

```java
@RequestMapping(
    value="/mac",
    method=RequestMethod.POST
)
public String postMac(){

    return "MAC2";

}
```

---

## Internal Flow

```
Incoming Request
      │
      ▼
URI = /mac
      │
      ▼
Check HTTP Method
      │
 ┌────┴────┐
 │         │
GET       POST
 │         │
 ▼         ▼
getMac() postMac()
```

---

## Key Observation

> URI alone does not identify a handler.
>
> URI + HTTP Method identifies the handler.

---

# Class-Level @RequestMapping

---

## Situation

Many methods share a common prefix.

Example:

```text
/iphone/model
/iphone/cost
/iphone/specs
```

Repeating:

```java
@GetMapping("/iphone/model")
@GetMapping("/iphone/cost")
```

becomes repetitive.

---

## Solution

Move common path to the class.

---

## Code

```java
@Controller
@RequestMapping("/ipad")
public class IpadController {

    @GetMapping("/cost")
    public String cost(){}

    @GetMapping("/model")
    public String model(){}

}
```

---

## Internal Flow

```
Class Mapping
    +
Method Mapping
       │
       ▼
Combined URI
```

---

## Result

Generated URLs:

```text
/ipad/cost
/ipad/model
```

---

# Specialized Mapping Annotations

Instead of:

```java
@RequestMapping(
    method=RequestMethod.GET
)
```

Spring provides shortcuts.

---

| Annotation | Equivalent To |
|------------|---------------|
| @GetMapping | @RequestMapping(method=GET) |
| @PostMapping | @RequestMapping(method=POST) |
| @PutMapping | @RequestMapping(method=PUT) |
| @DeleteMapping | @RequestMapping(method=DELETE) |

---

# Final Mental Model

```
Browser
   │
   ▼
Embedded Tomcat
   │
   ▼
DispatcherServlet
(Front Door)
   │
   ▼
HandlerMapping
(URL Navigator)
   │
   ▼
Controller Method
(Request Coordinator)
   │
   ▼
Service Layer
(Business Work)
   │
   ▼
ResponseBody / ViewResolver
(Response Preparation)
   │
   ▼
Browser
```

Spring MVC is essentially a highly organized request-routing system where Spring automatically discovers controllers, builds routing tables during startup, and efficiently delegates incoming requests to the correct handler methods.
