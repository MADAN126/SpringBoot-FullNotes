# Programming Language vs Framework

## Building an Application

Consider an E-Commerce application.

Users can:

- View Products
- Add Items to Cart
- Place Orders
- Make Payments

To build this application, we need to solve many problems.

```text
Show Pages

Handle User Requests

Process Business Logic

Store Data

Secure Accounts

Handle Errors
```

A programming language and a framework help solve these problems, but they play different roles.

---

# Programming Language

A programming language gives developers the ability to write instructions for a computer.

Without a programming language:

```text
No Logic

No Calculations

No Application
```

Examples:

```text
Java

Python

C#

JavaScript
```

---

## What Does a Programming Language Actually Do?

Suppose a customer places an order.

The application must:

```text
Calculate Total Amount

Apply Discount

Calculate Tax

Generate Final Price
```

Example:

```java
double total =
        amount - discount;
```

The programming language provides the syntax and rules needed to write this logic.

---

## Real Project Structure

A real project is not built using a single technology.

Different parts of the application use different tools.

```text
Application
      │
      ├── Frontend
      │
      ├── Backend
      │
      └── Database
```

---

## Frontend

Frontend is what users see and interact with.

Examples:

```text
Login Page

Register Page

Buy Now Button

Product Listing
```

Technologies:

```text
HTML

CSS

JavaScript

React

Angular
```

Example:

```text
User clicks

"Buy Now"
```

The click happens on the frontend.

---

## Backend

Backend handles the actual work.

Examples:

```text
Validate User

Calculate Price

Process Payment

Save Order
```

Technologies:

```text
Java

Python

C#
```

Example:

```text
User clicks Buy Now
        │
        ▼
Request Sent To Backend
        │
        ▼
Order Processed
```

---

## Database

Applications must store information permanently.

Examples:

```text
Customers

Products

Orders

Payments
```

Database operations:

```text
Insert Data

Update Data

Delete Data

Retrieve Data
```

Usually done using:

```text
SQL
```

---

# The Problem

Imagine building everything manually.

For every application, developers would repeatedly create:

```text
Login Logic

Security Logic

Database Connection Logic

Error Handling Logic

URL Mapping Logic
```

The same problems appear in almost every project.

---

# Framework

Instead of building these common features repeatedly, developers use a framework.

A framework provides ready-made solutions for common application problems.

```text
Common Problem
        │
        ▼
Framework Provides Solution
```

---

## Example

Without Framework:

```text
Create Login System

Create Security

Create Routing

Create Database Layer

Create Exception Handling
```

Everything must be built manually.

---

With Framework:

```text
Framework Already Provides
Most Of These Features
```

Developers focus on business requirements.

---

## What Problem Does a Framework Solve?

### URL Handling

Applications receive requests such as:

```text
/login

/register

/products

/orders
```

The framework decides:

```text
Which Code Should Handle
Which URL
```

Flow:

```text
Request
      │
      ▼
URL Received
      │
      ▼
Framework Finds Matching Logic
      │
      ▼
Response Returned
```

---

### Security

Most applications require:

```text
Login

Authentication

Authorization
```

Without a framework:

```text
Developer Creates Everything
```

With a framework:

```text
Many Security Features
Already Available
```

---

### Database Communication

Applications constantly communicate with databases.

Examples:

```text
Save Student

Save Employee

Update Customer

Find Product
```

Flow:

```text
Application
      │
      ▼
Framework
      │
      ▼
Database
```

Frameworks simplify this communication.

---

### Exception Handling

Applications should not crash when errors occur.

Example:

```text
Database Not Available
```

Instead of:

```text
Application Stops
```

Frameworks provide structured ways to handle failures.

---

### Caching

Some data is requested repeatedly.

Example:

```text
Popular Products
```

Instead of loading from the database every time:

```text
Database
      │
      ▼
Cache
      │
      ▼
User
```

Response becomes faster.

---

# Popular Frameworks

## Java

```text
Spring

Spring Boot

Hibernate

Struts

Quarkus
```

---

## Python

```text
Django

TurboGears

Dash
```

---

## PHP

```text
Laravel

Symfony

CodeIgniter

Slim

Lumen
```

---

## JavaScript

```text
React

Angular

Vue

Node.js
```

---

# Programming Language vs Framework

| Programming Language | Framework |
|----------|----------|
| Used to write application logic | Used to simplify application development |
| Provides syntax and rules | Provides ready-made solutions |
| Developer builds features manually | Many common features already available |
| Examples: Java, Python, C# | Examples: Spring, Django, React |

---

# Relationship Between Them

A framework does not replace a programming language.

A framework is built using a programming language.

Example:

```text
Java
   │
   ▼
Spring Framework
```

Spring exists because Java exists.

Without Java:

```text
No Spring Framework
```

---

# Complete Picture

```text
Programming Language
        │
        ▼
Write Business Logic
        │
        ▼
Framework
        │
        ▼
Provides Common Solutions
        │
        ▼
Faster Development
```

---

# Key Observation

Programming Language:

```text
Creates The Application
```

Framework:

```text
Helps Create The Application Faster
```

Think of it as:

```text
Programming Language
=
Raw Construction Materials
```

```text
Framework
=
Ready-Made Construction Tools
```

Both are required.

The language provides the foundation.

The framework reduces repetitive work and lets developers focus on solving business problems.
