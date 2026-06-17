# Spring Boot REST APIs — JSON Response Conversion, consumes & produces

---

# The Bigger Picture

So far, we understood:

```text
JSON Request
    ↓
@RequestBody
    ↓
Jackson
    ↓
Java Object
```

But REST communication is two-way.

Spring must also answer an equally important question:

> How does a Java object become JSON?

This chapter completes the REST cycle.

---

# Complete REST Communication Cycle

```text
Client Sends JSON
        │
        ▼
@RequestBody
        │
        ▼
Jackson
(JSON → Java)
        │
        ▼
Controller Executes
        │
        ▼
Controller Returns Java Object
        │
        ▼
Jackson
(Java → JSON)
        │
        ▼
Client Receives JSON
```

---

# Java Object → JSON Response

---

## Situation

A client requests product information.

Example:

```text
GET /load/product
```

The client expects:

```json
{
    "name":"iphone",
    "price":150000
}
```

---

## Problem

Controllers work with Java Objects.

Clients understand JSON.

Example:

Controller returns:

```java
ProductDetails
```

Browser expects:

```json
{
   ...
}
```

Who converts Java Objects into JSON?

---

## Solution

Spring automatically serializes Java Objects into JSON.

This process is called:

# Serialization

```text
Java Object
     ↓
JSON
```

using Jackson internally.

---

# ProductDetails POJO

```java
public class ProductDetails {

    private String name;
    private double price;
    private String companyName;
    private String contactEmail;
    private long contactNumber;

    public ProductDetails() {
    }

    public ProductDetails(
            String name,
            double price,
            String companyName,
            String contactEmail,
            long contactNumber) {

        this.name = name;
        this.price = price;
        this.companyName = companyName;
        this.contactEmail = contactEmail;
        this.contactNumber = contactNumber;
    }

    // getters and setters
}
```

---

# Endpoint Returning Java Object

```java
@GetMapping("/load/product")
public ProductDetails loadProductDetails() {

    ProductDetails details =
            new ProductDetails(
                    "iphone",
                    150000.00,
                    "apple",
                    "info@apple.com",
                    8826111377L);

    return details;
}
```

---

# Internal Flow

```text
Client Requests Product
        │
        ▼
DispatcherServlet
        │
        ▼
Controller Executes
        │
        ▼
Returns ProductDetails Object
        │
        ▼
@ResponseBody Behaviour
        │
        ▼
HttpMessageConverter
        │
        ▼
Jackson Serialization
        │
        ▼
JSON Generated
        │
        ▼
HTTP Response Sent
```

---

# Result

Spring automatically produces:

```json
{
    "name": "iphone",
    "price": 150000.0,
    "companyName": "apple",
    "contactEmail": "info@apple.com",
    "contactNumber": 8826111377
}
```

---

# Key Observation

> Returning Java Objects from REST endpoints is enough.

You never manually write JSON.

Spring + Jackson do it automatically.

---

# Serialization vs Deserialization

| Process | Direction | Example |
|----------|------------|-----------|
| Deserialization | JSON → Java | @RequestBody |
| Serialization | Java → JSON | Returning Objects |

---

# Mental Model

```text
Incoming Request
JSON
 ↓
Jackson
 ↓
Java Object

Business Logic

Java Object
 ↓
Jackson
 ↓
JSON
Outgoing Response
```

---

# HttpMessageConverter

---

## Situation

Spring must convert data formats.

Examples:

```text
JSON → Java
Java → JSON
XML → Java
Java → XML
```

---

## Problem

DispatcherServlet doesn't know how to parse JSON.

Controllers shouldn't do conversion manually.

---

## Solution

Spring delegates conversion to:

# HttpMessageConverter

---

# Internal Flow

```text
Request Body
      │
      ▼
HttpMessageConverter
      │
      ▼
Jackson
      │
      ▼
Java Object
```

Response:

```text
Java Object
      │
      ▼
HttpMessageConverter
      │
      ▼
Jackson
      │
      ▼
JSON Response
```

---

## Key Observation

> HttpMessageConverter is the bridge between HTTP payloads and Java objects.

---

# consumes Attribute

---

# Situation

Suppose an endpoint only understands JSON.

Example:

```text
POST /add/model
```

The server expects:

```json
{
   ...
}
```

---

## Problem

What happens if someone sends XML?

```xml
<Laptop>
    ...
</Laptop>
```

The endpoint cannot process it.

Need restrictions.

---

## Solution

Use:

# consumes

It tells Spring:

> "Accept only these request content types."

---

# JSON Only Endpoint

```java
@RequestMapping(
        path="/add/model",
        method=RequestMethod.POST,
        consumes="application/json")
public String addLaptopDetails(
        @RequestBody LaptopDetails details) {

    return "Added Successfully";

}
```

---

# Internal Flow

```text
Request Arrives
       │
       ▼
Content-Type Checked
       │
 ┌─────┴─────┐
 │           │
JSON       Other
 │           │
 ▼           ▼
Execute    Reject
Method
```

---

# Result

Allowed:

```http
Content-Type: application/json
```

Rejected:

```http
Content-Type: application/xml
```

---

# Error Response

If XML is sent:

```text
415 Unsupported Media Type
```

Example:

```json
{
    "error":"Unsupported Media Type"
}
```

---

## Why?

Because:

```text
Content-Type
       ≠
consumes
```

---

# LaptopDetails POJO

```java
public class LaptopDetails {

    private String lapName;
    private double cost;
    private int modelYear;

    // getters and setters
}
```

---

# XML Support

---

# Situation

Some systems still communicate using XML.

Examples:

```text
Legacy Enterprise Systems
SOAP Integrations
Older Government APIs
```

---

## Problem

Spring Boot defaults to JSON.

XML isn't automatically configured.

---

## Solution

Add XML support.

---

# Required Dependencies

```xml
<dependency>
    <groupId>
        com.fasterxml.jackson.dataformat
    </groupId>

    <artifactId>
        jackson-dataformat-xml
    </artifactId>
</dependency>
```

```xml
<dependency>
    <groupId>
        org.glassfish.jaxb
    </groupId>

    <artifactId>
        jaxb-runtime
    </artifactId>
</dependency>
```

---

# What Happens Internally?

```text
Application Starts
       │
       ▼
XML Converter Detected
       │
       ▼
HttpMessageConverter Extended
       │
       ▼
XML Parsing Enabled
```

---

# XML Only Endpoint

```java
@RequestMapping(
        path="/add/model",
        method=RequestMethod.POST,
        consumes="application/xml")
public String addLaptopDetails(
        @RequestBody LaptopDetails details) {

    return "Added Successfully";

}
```

---

# Internal Flow

```text
Request
   │
Content-Type ?
   │
 ┌─────┴─────┐
 │           │
XML       JSON
 │           │
 ▼           ▼
Allowed   Rejected
```

---

# Result

Only XML requests succeed.

---

# Supporting JSON and XML Together

---

## Situation

Some clients send JSON.

Others send XML.

Need flexibility.

---

## Solution

Allow multiple media types.

---

# Code

```java
@RequestMapping(
        path="/add/model",
        method=RequestMethod.POST,
        consumes={
            "application/json",
            "application/xml"
        })
public String addLaptopDetails(
        @RequestBody LaptopDetails details) {

    return "Added Successfully";

}
```

---

# Internal Flow

```text
Request Arrives
       │
       ▼
Content-Type Checked
       │
 ┌─────┼─────┐
 │     │     │
JSON XML Other
 │     │     │
 ▼     ▼     ▼
OK    OK   Reject
```

---

# Result

Accepted:

```http
application/json
```

```http
application/xml
```

Rejected:

Anything else.

---

# MediaType Constants

---

## Situation

Hardcoding strings can cause mistakes.

Example:

```java
"application/json"
```

Typo.

---

## Solution

Spring provides:

# MediaType

---

# Code

Instead of:

```java
consumes={
    "application/json",
    "application/xml"
}
```

Use:

```java
consumes={
    MediaType.APPLICATION_JSON_VALUE,
    MediaType.APPLICATION_XML_VALUE
}
```

---

## Benefits

```text
Compile-Time Safety
Better Readability
Avoid Typographical Errors
IDE Auto Completion
```

---

# produces Attribute

---

# Situation

Clients may request different response formats.

Example:

```text
JSON
XML
```

Need control over response generation.

---

## Problem

Without restrictions:

Spring decides automatically.

Sometimes this isn't desirable.

---

## Solution

Use:

# produces

It tells Spring:

> "Generate response only in these formats."

---

# XML Response Endpoint

```java
@RequestMapping(
        path="/model/2345",
        method=RequestMethod.GET,
        produces=MediaType.APPLICATION_XML_VALUE)
public LaptopDetails getLaptopDetails() {

    LaptopDetails lap = new LaptopDetails();

    lap.setCost(80000.00);
    lap.setLapName("Thinkpad");
    lap.setModelYear(2023);

    return lap;
}
```

---

# Internal Flow

```text
Controller Returns Java Object
         │
         ▼
Produces Checked
         │
         ▼
XML Converter Selected
         │
         ▼
Java Object Serialized
         │
         ▼
XML Response Generated
```

---

# Result

Response becomes:

```xml
<LaptopDetails>
    <lapName>Thinkpad</lapName>
    <cost>80000.0</cost>
    <modelYear>2023</modelYear>
</LaptopDetails>
```

---

# produces vs consumes

| Attribute | Controls | Uses |
|------------|-----------|-------|
| consumes | Request Body | What client can send |
| produces | Response Body | What server returns |

---

# Mental Model

```text
Client
   │
Content-Type
(JSON/XML)
   │
   ▼
consumes
(Request Validation)
   │
   ▼
@RequestBody
   │
   ▼
Jackson/XML Converter
   │
   ▼
Java Objects
   │
Business Logic
   │
Java Objects
   │
   ▼
produces
(Response Format Selection)
   │
   ▼
Jackson/XML Converter
   │
   ▼
JSON/XML Response
   │
   ▼
Client
```

---

# Final Mental Model

Spring REST APIs act as intelligent translators.

```text
Incoming Payload
       │
       ▼
consumes
       │
       ▼
HttpMessageConverter
       │
       ▼
Jackson/XML
       │
       ▼
Java Objects
       │
Business Logic
       │
Java Objects
       │
       ▼
HttpMessageConverter
       │
       ▼
Jackson/XML
       │
       ▼
produces
       │
       ▼
Outgoing Payload
```

Once you understand this flow, REST APIs stop feeling magical.

Spring is simply:

- validating what formats clients send,
- translating payloads into Java objects,
- executing business logic,
- translating Java objects back into payloads,
- and delivering them in the format the client expects.
