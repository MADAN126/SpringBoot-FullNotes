# SPRING BOOT WEB + SPRING MVC — UNDERSTANDING-FIRST NOTES

---

# 1. WHY SPRING MVC EXISTS

## Situation
Web applications constantly deal with:
- HTTP requests
- Business logic execution
- Response generation (HTML / JSON)

## Problem
Without structure:
- Request handling logic spreads everywhere
- UI + backend logic gets mixed
- Code becomes unscalable and hard to debug

## Solution
Spring MVC introduces a controlled flow:
- One central entry point
- Clear separation of responsibilities
- Standard pipeline for every request

---

## INTERNAL IDEA

```
HTTP REQUEST
    ↓
DispatcherServlet (Single Entry Point)
    ↓
Controller
    ↓
Business Logic
    ↓
Response (JSON / View)
```

---

# 2. MVC ARCHITECTURE

## Situation
Applications need clean separation of responsibilities.

## Problem
Without separation:
- UI logic and data logic mix
- Changes break unrelated parts
- Maintenance becomes risky

## Solution → MVC Split

### MODEL
- Represents data + business state
- Talks to database

Think: “What data exists?”

---

### VIEW
- Presentation layer (UI / JSON output)

Think: “How data is shown”

---

### CONTROLLER
- Receives request
- Calls model/business logic
- Returns response

Think: “Traffic controller”

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

# 3. WHY SPRING MVC IS POWERFUL

## Key Idea
Spring removes manual web plumbing.

### It provides:
- Request routing system
- Annotation-based mapping
- Automatic JSON conversion
- Embedded server support

---

## BENEFIT TABLE

| Feature | Why it matters |
|--------|----------------|
| Separation of concerns | Clean architecture |
| Embedded server | No external setup |
| Annotations | Less configuration |
| Reusability | Logic is independent |

---

# 4. DISPATCHERSERVLET (CORE OF SPRING MVC)

## Situation
Every request must enter the system somewhere.

## Problem
Without a central controller:
- Every controller must handle routing itself
- No unified control flow

## Solution
DispatcherServlet acts as:

> FRONT CONTROLLER

---

## WHAT IT DOES

- Receives ALL requests
- Finds correct controller
- Sends request to it
- Handles response return

---

## INTERNAL FLOW

```
REQUEST
  ↓
DispatcherServlet
  ↓
HandlerMapping (find controller)
  ↓
Controller method execution
  ↓
Response creation
  ↓
Client
```

---

## KEY IDEA
DispatcherServlet = “Traffic control system of Spring MVC”

---

# 5. SPRING BOOT WEB FLOW

## Situation
Manual server setup is slow and repetitive.

## Problem
Developers must configure:
- Server
- Dependencies
- Web infrastructure

## Solution
Spring Boot automates everything.

---

## FLOW

```
Spring Boot Application
    ↓
Auto Configuration
    ↓
Embedded Tomcat Starts
    ↓
Application Runs
```

---

# 6. SERVER DEFAULT BEHAVIOR

## Default Setup

```
Port: 8080
Context Path: /
```

---

## CUSTOM CONFIG

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

# 7. SIMPLE CONTROLLER EXAMPLE

## Situation
Expose a URL endpoint.

## Problem
Need to map URL → method.

## Solution
Use Spring annotations.

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
DispatcherServlet
    ↓
Controller method found
    ↓
Method executed
    ↓
String returned
    ↓
@ResponseBody sends raw response
```

---

## KEY POINTS

- @Controller → handles requests
- @GetMapping → maps URL
- @ResponseBody → returns raw output (not view)

---

# 8. SPRING MVC COMPLETE ARCHITECTURE

## FULL FLOW

```
Client Request
    ↓
DispatcherServlet
    ↓
HandlerMapping
    ↓
Controller
    ↓
Business Logic
    ↓
Response Builder
    ↓
Client Response
```

---

# 9. FINAL UNDERSTANDING MODEL

Spring MVC is basically:

> A single-entry controlled system where every request is routed, processed, and returned through a strict pipeline.

---

## MASTER FLOW

```
Request
 ↓
Spring Boot Server
 ↓
DispatcherServlet
 ↓
Controller
 ↓
Logic
 ↓
Response
```
