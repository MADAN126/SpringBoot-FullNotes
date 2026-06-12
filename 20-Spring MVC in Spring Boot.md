# SPRING WEB MVC + SPRING BOOT — UNDERSTANDING-FIRST NOTES

---

# 1. WHY SPRING MVC EXISTS

## Situation
Web applications constantly need to:
- Accept HTTP requests
- Process business logic
- Return responses (HTML / JSON)

---

## Problem (Without MVC thinking)
If everything is written directly:
- Request handling becomes scattered
- UI + business logic gets mixed
- Code becomes unmaintainable

---

## Solution
Spring MVC introduces a structured web flow:

- One central entry point
- Clear separation of responsibilities
- Standard request → response pipeline

---

## CORE IDEA

```
HTTP REQUEST
    ↓
DispatcherServlet (Front Controller)
    ↓
Controller
    ↓
Model (Business/Data Logic)
    ↓
Response (View / JSON)
```

---

# 2. WHAT SPRING MVC REALLY IS

Spring MVC is:

> A web framework built on Servlet API that applies MVC architecture to structure web applications.

---

## WHY IT IS CALLED MVC

Because it forces separation into 3 parts:

### MODEL
- Holds application data + business state
- Talks to database
- Example: Customer object, Student object

👉 Focus: “What data exists?”

---

### VIEW
- Presentation layer (UI / response output)
- Includes HTML pages or JSON output structure

👉 Focus: “How data is shown”

---

### CONTROLLER
- Receives request
- Coordinates between Model and View
- Sends response back

👉 Focus: “What should happen when request comes?”

---

## MVC FLOW

```
USER
 ↓
CONTROLLER
 ↓
MODEL (data processing)
 ↓
CONTROLLER
 ↓
VIEW / RESPONSE
 ↓
USER
```

---

# 3. WHY SPRING MVC BECAME POPULAR

## Problem it solves
Earlier web apps required manual request handling and mapping.

---

## Spring MVC advantages (real meaning)

### 1. Separation of roles
Each component has a fixed responsibility:
- Controller → request handling
- Model → data logic
- ViewResolver → response rendering

---

### 2. Lightweight deployment
Runs on servlet containers like Tomcat without heavy setup.

---

### 3. Powerful configuration
- Easy wiring between components
- Controllers can directly interact with services and validators

---

### 4. Rapid development
Less boilerplate → faster development cycles

---

### 5. Reusable business logic
Business logic is not tied to UI → can be reused

---

### 6. Easy testing
Plain JavaBeans can be tested using setters

---

### 7. Flexible mapping
Annotations reduce manual URL mapping complexity

---

# 4. SPRING BOOT WEB IDEA

## Situation
Spring MVC alone requires configuration and setup.

---

## Problem
Developers must manually configure:
- Server
- Dependencies
- Web setup

---

## Solution: Spring Boot

Spring Boot removes setup friction:

- Auto configuration
- Embedded server
- Ready-to-run application

---

## INTERNAL FLOW

```
Spring Boot App
    ↓
Auto Configuration
    ↓
Embedded Tomcat / Jetty / Undertow
    ↓
Application Starts
```

---

## KEY IDEA

> Spring Boot = Spring MVC + Auto Configuration + Embedded Server

---

# 5. SERVER BEHAVIOR IN SPRING BOOT

## Default behavior

```
Port: 8080
Context Path: /
```

---

## Custom configuration

```properties
server.port=8899
server.servlet.context-path=/hello
```

---

## RESULT

```
http://localhost:8899/hello
```

---

# 6. HELLO WORLD CONTROLLER (REAL EXECUTION)

## Situation
Expose a web endpoint.

---

## Problem
Need to connect URL → Java method

---

## Solution
Spring MVC annotations handle mapping automatically.

---

## CODE

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

## INTERNAL EXECUTION FLOW

```
/world request
    ↓
DispatcherServlet receives request
    ↓
HandlerMapping finds controller method
    ↓
printHelloWorld() executes
    ↓
String returned
    ↓
@ResponseBody converts it into HTTP response
```

---

## KEY INSIGHT

- @Controller → marks request handler class  
- @GetMapping → maps URL to method  
- @ResponseBody → sends raw response (not view page)

---

# 7. DISPATCHERSERVLET (CORE ENGINE)

## Situation
Every request must enter somewhere central.

---

## Problem
Without central control:
- No unified routing system
- Every controller would manage its own entry

---

## Solution
Spring introduces:

> DispatcherServlet = FRONT CONTROLLER

---

## WHAT IT DOES

- Receives ALL HTTP requests
- Finds correct controller using mapping
- Delegates execution
- Handles response pipeline

---

## INTERNAL FLOW

```
REQUEST
  ↓
DispatcherServlet
  ↓
HandlerMapping
  ↓
Controller
  ↓
Business Logic
  ↓
Response returned
```

---

## KEY IDEA

DispatcherServlet = “Single brain controlling all web requests”

---

# 8. SPRING MVC FULL ARCHITECTURE

## High-level flow

```
Client
 ↓
DispatcherServlet
 ↓
HandlerMapping
 ↓
Controller
 ↓
Model (Business Logic)
 ↓
ViewResolver / Response builder
 ↓
Client Response
```

---

# 9. SPRING MVC IN ONE MENTAL MODEL

Spring MVC is basically:

> A structured pipeline where every request enters one controlled system, gets routed, processed, and returned in a clean flow.

---

## FINAL FLOW

```
Request
 ↓
Spring Boot Server
 ↓
DispatcherServlet
 ↓
Controller
 ↓
Business Logic
 ↓
Response
```

---
