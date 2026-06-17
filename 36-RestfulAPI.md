# REST API / RESTful Web Services — Understanding First Notes

---

# Why REST Exists

## Situation

Imagine these systems:

- Flipkart App
- ICICI Banking System
- Payment Gateway
- Weather Service
- Mobile App
- Website Frontend

All of them need to communicate.

Example:

```text
Flipkart
   │
   ▼
ICICI Payment System
   │
   ▼
Returns Payment Status
```

---

## Problem

Without a common way to communicate:

- Every system would invent its own communication style.
- Integration becomes difficult.
- Technology differences become barriers.

Example:

```text
Java Application
      ❌
Cannot understand
      ❌
Python Application
```

without agreed rules.

---

## Solution

Use APIs.

And among API styles, REST became the most popular approach.

---

# What is an API?

---

## Situation

You want to use ICICI's payment functionality.

You don't know:

- How their database works.
- Their internal business logic.
- Their implementation.

You only need:

```text
Card Number
Name
CVV
Amount
```

and receive:

```text
Payment Success/Failure
```

---

## Problem

Exposing internal implementation is dangerous.

---

## Solution

Expose only a contract.

That contract is called:

# API (Application Programming Interface)

---

# Mental Model

Think of API as:

```text
Restaurant Menu
```

You don't enter the kitchen.

You simply say:

```text
"I want this."
```

Kitchen prepares internally.

Returns result.

---

# API Flow

```text
Client
   │
Request
   │
▼
API Contract
   │
▼
Internal Logic
   │
▼
Response
```

---

# REST

---

## Situation

Many APIs existed.

SOAP, RPC, CORBA...

Internet applications needed something:

- Simpler
- Faster
- Scalable
- Technology independent

---

## Solution

REST.

---

# What REST Actually Is

REST stands for:

```text
Representational State Transfer
```

But don't memorize that.

Understand this instead:

> REST is a set of architectural guidelines for designing APIs over HTTP.

---

# Important Observation

REST is NOT:

```text
❌ Protocol
❌ Software
❌ Library
❌ Standard
```

REST IS:

```text
✅ Architectural Style
✅ Design Principles
```

---

# Mental Model

REST says:

```text
Resources should be exposed using URLs.

HTTP methods should describe actions.

Responses should contain representations
of those resources.
```

---

# REST Communication

```text
Client
   │
HTTP Request
   │
▼
REST API
   │
Business Logic
   │
Database
   │
▼
HTTP Response
```

---

# Resources

---

## Situation

What exactly are clients asking for?

---

## Answer

Resources.

Resources are the actual data.

Examples:

```text
User
Product
Order
Payment
Image
Video
Invoice
```

---

# Resource Examples

```text
/users/10

/orders/100

/products/55
```

---

# Mental Model

Think:

```text
REST API
     =
Resource Manager
```

It provides access to resources.

---

# Clients

Clients consume resources.

Examples:

```text
Web Browser

Mobile App

Angular Frontend

React Frontend

Another Backend System

Postman
```

---

# Client-Server Flow

```text
Client
   │
Needs Resource
   │
▼
REST API
(Server)
   │
Processes Request
   │
▼
Returns Resource
```

---

# REST Principles

These principles make REST powerful.

---

# 1. Uniform Interface

---

## Situation

Suppose one API returns:

```json
{
  "name":"John"
}
```

Another returns:

```xml
<name>John</name>
```

Another returns:

```text
John
```

Randomly.

---

## Problem

Clients become confused.

Every API behaves differently.

---

## Solution

Expose resources consistently.

This is called:

# Uniform Interface

---

# Mental Model

Think:

```text
Same rules.

Same style.

Predictable behavior.
```

---

# Example

Fetching a user:

```text
GET /users/10
```

Always means:

```text
Fetch User 10
```

---

# Flow

```text
Resource
   │
Representation
   │
JSON/XML
   │
Client Understands
```

---

# Representation

Server may internally store:

```text
Database Tables
```

but expose:

```json
{
  "id":10,
  "name":"John"
}
```

Client sees representation.

Not internal implementation.

---

# 2. Statelessness

---

## Situation

User sends:

```text
Request 1
```

Then:

```text
Request 2
```

---

## Problem

Should server remember previous requests?

If yes:

```text
Memory Usage ↑

Complexity ↑

Scalability ↓
```

---

## Solution

REST says:

# Be Stateless

---

# Meaning

Every request must contain everything needed.

---

# Example

Bad Design:

```text
Request 1:
Login

Request 2:
Transfer Money

Server remembers login state.
```

---

REST prefers:

```text
Each Request
     │
Contains Authentication
     │
Server Processes Independently
```

---

# Flow

```text
Request
   │
Authentication
   │
Data
   │
▼
Process
   │
Forget Everything
```

---

# Benefits

```text
Easier Scaling

Load Balancing

Less Memory Usage

Fault Tolerance
```

---

# 3. Layered System

---

## Situation

Large systems have:

- Security
- Logging
- Load Balancers
- Business Services
- Databases

---

## Problem

Client shouldn't know all this complexity.

---

## Solution

Use layers.

---

# Flow

```text
Client
   │
▼
Security Layer
   │
▼
Gateway Layer
   │
▼
Business Layer
   │
▼
Database Layer
```

---

# Client Sees

Only this:

```text
Client
   │
REST API
```

---

# Hidden Internal Architecture

Invisible to clients.

---

# Benefits of REST APIs

---

# 1. Scalability

Stateless requests allow:

```text
Request 1 → Server A

Request 2 → Server B

Request 3 → Server C
```

No dependency.

Easy scaling.

---

# 2. Flexibility

Frontend changes?

```text
React → Angular
```

Backend unaffected.

Database changes?

```text
MySQL → PostgreSQL
```

API contract remains.

---

# 3. Platform Independence

Java server:

```text
↓
Can serve
↓
React Client
Android App
Python App
iOS App
```

---

# How REST APIs Work

---

# Complete Flow

```text
Client
   │
1. Sends Request
   │
▼
Server
   │
2. Authenticates Request
   │
▼
Processes Logic
   │
▼
Database Access
   │
▼
3. Generates Response
   │
▼
Client Receives Result
```

---

# REST Request Components

---

# 1. URI (Endpoint)

---

## Situation

Server has thousands of resources.

How do we identify one?

---

## Solution

URI.

---

# Examples

```text
/users

/users/100

/orders/55
```

---

# Mental Model

URI is:

```text
Address of Resource
```

---

# Flow

```text
URI
   │
Identifies Resource
   │
Server Finds It
```

---

# 2. HTTP Methods

HTTP method tells server:

> What should I do?

---

# GET

## Meaning

Fetch.

---

Example

```http
GET /users/100
```

Meaning:

```text
Give User 100
```

---

# POST

## Meaning

Create.

---

Example

```http
POST /users
```

Meaning:

```text
Create User
```

---

# Important Observation

POST is NOT idempotent.

---

Example

Sending twice:

```text
POST User

POST User
```

may create:

```text
2 Users
```

---

# PUT

## Meaning

Update.

---

Example

```http
PUT /users/100
```

Meaning:

```text
Replace User 100
```

---

# Important Observation

PUT is idempotent.

---

Example

Sending same PUT repeatedly:

```text
PUT User 100

PUT User 100
```

Result:

```text
Still same updated user.
```

---

# DELETE

## Meaning

Remove Resource.

---

Example

```http
DELETE /users/100
```

Meaning:

```text
Delete User 100
```

---

# CRUD Mapping

| Database Operation | HTTP Method |
|---|---|
| Create | POST |
| Read | GET |
| Update | PUT |
| Delete | DELETE |

---

# HTTP Headers

---

## Why?

Need metadata.

---

# Examples

```http
Content-Type: application/json

Authorization: Bearer token

Accept: application/json
```

---

# Mental Model

Headers describe:

```text
How to understand the request.
```

---

# Request Body

Needed when sending data.

---

Example

```http
POST /users
```

Body:

```json
{
    "name":"John",
    "age":30
}
```

---

# Parameters

REST supports additional information.

---

# Path Parameters

Identify resource.

Example:

```text
/users/100
```

---

# Query Parameters

Filter resources.

Example:

```text
/users?city=Chennai
```

---

# Cookie Parameters

Carry authentication/session information.

Example:

```text
Session ID
```

---

# REST Response Components

---

# 1. Status Code

---

# Mental Model

Status code answers:

```text
Did my request succeed?
```

---

# Categories

---

## 2XX

Success.

---

Examples:

| Code | Meaning |
|---|---|
| 200 | Success |
| 201 | Resource Created |

---

## 3XX

Redirection.

---

Example:

```text
Moved Permanently
```

---

## 4XX

Client Mistakes.

---

Examples:

| Code | Meaning |
|---|---|
| 400 | Bad Request |
| 404 | Resource Not Found |

---

## 5XX

Server Errors.

---

Examples:

```text
500 Internal Server Error
```

---

# Status Flow

```text
Request
   │
Processed
   │
▼
Status Code
   │
Success/Error
```

---

# 2. Response Body

Contains actual data.

---

Example

```json
{
    "name":"John",
    "age":30
}
```

---

# Flow

```text
Database Data
   │
Representation
   │
JSON/XML
   │
Response Body
```

---

# 3. Response Headers

Metadata about response.

---

Examples

```http
Content-Type: application/json

Date: Tue

Server: Apache
```

---

# JSON Representation

Example:

```json
{
    "name":"John",
    "age":30
}
```

---

# Why JSON Became Popular

```text
Human Readable

Machine Readable

Lightweight

Language Independent

Easy Parsing
```

---

# Complete REST API Lifecycle

```text
Client
   │
HTTP Request
   │
URI
HTTP Method
Headers
Body
Parameters
   │
▼
REST API Server
   │
Authentication
   │
Business Logic
   │
Database Access
   │
Generate Representation
   │
▼
HTTP Response
   │
Status Code
Headers
JSON/XML Body
   │
▼
Client
```

---

# Final Mental Model

Think of REST like this:

```text
REST is not about code.

REST is about communication discipline.

Resources have addresses.

HTTP methods describe intent.

Each request is independent.

Representations hide internal implementation.

Responses clearly communicate success or failure.

Clients and servers remain loosely coupled.
```

An experienced developer sees REST as:

```text
A predictable contract
that allows independent systems,
written in different technologies,
to communicate reliably at internet scale.
```
