# SPRING MVC + SPRING BOOT (COMPLETE UNDERSTANDING-FIRST NOTES)

# 1. WHY SPRING MVC EXISTS

## Situation
Web applications handle HTTP requests like:
- /login
- /products
- /orders

Each request requires routing, processing, and response.

## Problem
Without Spring MVC:
- Every request handled manually (Servlets)
- URL mapping written repeatedly
- UI + backend tightly coupled
- No central control flow

## Solution
Spring MVC introduces:
- Single entry point (DispatcherServlet)
- Automatic request routing
- Clear separation of responsibilities

## CORE IDEA

+------------------------------+
| CLIENT                       |
+--------------+---------------+
               |
               v
+------------------------------+
| DISPATCHER SERVLET          |
| (Front Controller)          |
+--------------+---------------+
               |
               v
+------------------------------+
| HANDLER MAPPING             |
| URL → Method                |
+--------------+---------------+
               |
               v
+------------------------------+
| CONTROLLER                  |
| Business Logic              |
+--------------+---------------+
               |
               v
+------------------------------+
| MODEL                       |
| Data Processing             |
+--------------+---------------+
               |
               v
+------------------------------+
| RESPONSE                    |
+--------------+---------------+
               |
               v
+------------------------------+
| CLIENT                      |
+------------------------------+

# 2. MVC ARCHITECTURE

## Situation
Every web app needs:
- Data handling
- UI rendering
- Request processing

## Problem
If mixed:
- Code becomes unmaintainable
- UI changes break backend
- Tight coupling

## Solution

- Controller → handles requests
- Model → business logic + data
- View → UI output

## FLOW

+------------+
| CLIENT     |
+-----+------+
      |
      v
+------------+
| CONTROLLER |
+-----+------+
      |
      v
+------------+
| MODEL      |
+-----+------+
      |
      v
+------------+
| VIEW       |
+-----+------+
      |
      v
+------------+
| CLIENT     |
+------------+

# 3. SPRING MVC INTERNAL WORKING

## Problem
Multiple controllers exist:
- UserController
- OrderController

How does Spring decide?

## Solution
DispatcherServlet is the single entry point.

## INTERNAL FLOW

+------------------------------+
| CLIENT                       |
+--------------+---------------+
               |
               v
+------------------------------+
| DISPATCHER SERVLET          |
+--------------+---------------+
               |
               v
+------------------------------+
| HANDLER MAPPING             |
+--------------+---------------+
               |
               v
+------------------------------+
| CONTROLLER METHOD           |
+--------------+---------------+
               |
               v
+------------------------------+
| RESPONSE CONVERTER          |
+--------------+---------------+
               |
               v
+------------------------------+
| CLIENT                      |
+------------------------------+

# INTERNAL TRUTH
- Spring scans controllers at startup
- Builds URL → method mapping table
- Uses reflection to invoke methods

# 4. SPRING BOOT ROLE

## Problem
Spring MVC needs heavy configuration:
- XML setup
- Server setup
- Manual wiring

## Solution
Spring Boot provides:
- Auto configuration
- Embedded server
- Starter dependencies

## STARTUP FLOW

+------------------------------+
| SPRING BOOT APP             |
+--------------+---------------+
               |
               v
+------------------------------+
| AUTO CONFIGURATION          |
+--------------+---------------+
               |
               v
+------------------------------+
| EMBEDDED TOMCAT SERVER      |
+--------------+---------------+
               |
               v
+------------------------------+
| APPLICATION RUNNING         |
+------------------------------+

# 5. CONTROLLER EXECUTION FLOW

```java
@Controller
class HelloWorldController {

    @GetMapping("/world")
    @ResponseBody
    public String hello() {
        return "Hello Spring MVC";
    }
}
EXECUTION

+------------------------------+
| CLIENT (/world) |
+--------------+---------------+
|
v
+------------------------------+
| DISPATCHER SERVLET |
+--------------+---------------+
|
v
+------------------------------+
| @GetMapping MATCHED |
+--------------+---------------+
|
v
+------------------------------+
| METHOD EXECUTION |
+--------------+---------------+
|
v
+------------------------------+
| RESPONSE CONVERTER |
+--------------+---------------+
|
v
+------------------------------+
| CLIENT |
+------------------------------+

FINAL MENTAL MODEL

SPRING MVC = REQUEST PROCESSING ENGINE

CLIENT → DISPATCHER → MAPPING → CONTROLLER → RESPONSE
