# Bean Scopes in Spring

## Situation

Spring creates and manages objects (Beans) inside the IoC Container.

When Spring creates a bean, one important question arises:

> How many objects should Spring create for this bean?

Should Spring create:

- Only one object?
- A new object every time?
- One object per HTTP request?
- One object per user session?

This behavior is controlled by **Bean Scope**.

---

## Problem

Suppose 100 parts of the application request the same bean.

```java
context.getBean("productDetails");
```

Should Spring:

- Return the same object every time?
- Create a new object every time?

Spring needs a rule to decide.

---

## Solution

Spring associates every bean with a **Scope**.

Scope defines:

- Bean lifecycle
- Bean visibility
- Number of bean objects created

---

# Available Bean Scopes

| Scope | Meaning |
|---------|----------|
| singleton | One object for entire container |
| prototype | New object every request |
| request | One object per HTTP request |
| session | One object per HTTP session |
| application | One object per ServletContext |
| globalsession | One object per Global Session |

---

## Important Observation

Only these scopes work in normal Core Spring applications:

```text
singleton
prototype
```

The remaining scopes require:

```text
Web Application
+
Web-Aware ApplicationContext
```

---

# Defining Scope

## XML Configuration

```xml
<bean id="productDetails"
      class="com.amazon.products.ProductDetails"
      scope="singleton">
</bean>
```

---

## Annotation Configuration

Spring provides:

```java
@Scope
```

annotation.

```java
@Scope("singleton")
```

or

```java
@Scope("prototype")
```

---

# Understanding Singleton Scope

## Situation

We need only one ProductDetails object.

Regardless of how many times the bean is requested.

---

## Problem

Without a scope rule, Spring doesn't know whether to reuse or recreate objects.

---

## Solution

Use Singleton Scope.

```java
@Configuration
public class BeansConfigurationThree {

    @Scope("singleton")
    @Bean("productDetails")
    ProductDetails getProductDetails() {

        return new ProductDetails();
    }
}
```

---

## What Spring Understands

```text
Create ProductDetails Only Once

Store It In Container

Return Same Object Every Time
```

---

## Internal Flow

```text
Application Starts
        │
        ▼
refresh()
        │
        ▼
@Bean Method Executed
        │
        ▼
ProductDetails Object Created
        │
        ▼
Stored Inside Container
        │
        ▼
getBean()
        │
        ▼
Same Object Returned
        │
        ▼
getBean()
        │
        ▼
Same Object Returned Again
```

---

## Test Code

```java
ProductDetails productOne =
        (ProductDetails) context.getBean("productDetails");

ProductDetails productTwo =
        (ProductDetails) context.getBean("productDetails");

System.out.println(productOne);
System.out.println(productTwo);
```

---

## Output

```text
com.amazon.products.ProductDetails@58e1d9d

com.amazon.products.ProductDetails@58e1d9d
```

---

## Why Same Hashcode?

```text
productOne
      │
      ▼
ProductDetails Object
      ▲
      │
productTwo
```

Both variables point to the same object.

---

## Key Observation

One Bean Definition

↓

One Object

↓

Same Object Returned Every Time

---

# Default Scope

## Situation

No scope specified.

```java
@Bean
ProductDetails getProductDetails() {
    return new ProductDetails();
}
```

---

## What Spring Does?

Spring automatically assumes:

```java
@Scope("singleton")
```

---

## Key Observation

Singleton is Spring's default scope.

---

# Understanding Prototype Scope

## Situation

We want a fresh object every time.

Example:

Every request should receive a completely new ProductDetails object.

---

## Problem

Singleton returns the same object repeatedly.

Changes made by one user may affect another reference.

---

## Solution

Use Prototype Scope.

```java
@Configuration
public class BeansConfigurationThree {

    @Scope("prototype")
    @Bean("productTwoDetails")
    ProductDetails getProductTwoDetails() {

        return new ProductDetails();
    }
}
```

---

## What Spring Understands

```text
Do NOT Reuse Object

Create New Object
Whenever getBean() Is Called
```

---

## Internal Flow

```text
getBean()
      │
      ▼
Create New ProductDetails
      │
      ▼
Return Object #1


getBean()
      │
      ▼
Create New ProductDetails
      │
      ▼
Return Object #2
```

---

## Test Code

```java
ProductDetails productThree =
        (ProductDetails) context.getBean("productTwoDetails");

ProductDetails productFour =
        (ProductDetails) context.getBean("productTwoDetails");

System.out.println(productThree);

System.out.println(productFour);
```

---

## Output

```text
com.amazon.products.ProductDetails@12591ac8

com.amazon.products.ProductDetails@5a7fe64f
```

---

## Why Different Hashcodes?

Because Spring created:

```text
Object #1
```

for first request

and

```text
Object #2
```

for second request.

---

## Visual Representation

```text
First getBean()
      │
      ▼
ProductDetails@12591ac8


Second getBean()
      │
      ▼
ProductDetails@5a7fe64f
```

Different objects.

---

## Key Observation

One Bean Definition

↓

Multiple Objects

↓

New Object For Every getBean()

---

# Singleton vs Prototype

| Feature | Singleton | Prototype |
|----------|------------|------------|
| Objects Created | One | Many |
| Default Scope | Yes | No |
| Object Reused | Yes | No |
| Memory Usage | Low | Higher |
| getBean() Result | Same Object | New Object |

---

## Internal Comparison

### Singleton

```text
Container
      │
      ▼
ProductDetails Object
      ▲
      │
All getBean() Calls
```

---

### Prototype

```text
getBean()
      │
      ▼
Object #1


getBean()
      │
      ▼
Object #2


getBean()
      │
      ▼
Object #3
```

---

# Web-Specific Scopes

These scopes work only inside web applications.

---

## Request Scope

### Meaning

One object per HTTP Request.

```text
Request 1
      │
      ▼
Bean Object #1

Request 2
      │
      ▼
Bean Object #2
```

---

## Session Scope

### Meaning

One object per user session.

```text
User A Session
       │
       ▼
Bean Object #1

User B Session
       │
       ▼
Bean Object #2
```

---

## Application Scope

### Meaning

One bean object for entire ServletContext.

Shared across all users.

---

## Global Session Scope

### Meaning

Used mainly in Portlet applications.

Rarely used in modern Spring Boot applications.

---

# Why @Component Was Introduced

## Situation

So far every bean must be explicitly registered.

Example:

```java
@Bean("productDetails")
ProductDetails getProductDetails() {

    return new ProductDetails();
}
```

---

## Problem

For large applications:

- Hundreds of classes exist.
- Hundreds of bean methods must be written.
- Configuration classes become huge.

Spring needed a way to automatically discover beans.

---

## Solution

Spring introduced:

```java
@Component
```

---

# Understanding @Component

## What Does It Mean?

When Spring sees:

```java
@Component
```

it understands:

> This class should become a Spring Bean.

---

## Example

```java
@Component("product")
public class ProductDetails {

    private String pname;
    private double price;

}
```

---

## What Spring Should Do

```text
Scan Class
      │
      ▼
Find @Component
      │
      ▼
Create Object
      │
      ▼
Register Bean
      │
      ▼
Store In Container
```

---

# Why Exception Occurred?

## Situation

Class contains:

```java
@Component("product")
public class ProductDetails {

}
```

Main method:

```java
ProductDetails details =
        (ProductDetails) context.getBean("product");
```

---

## Expected Result

Spring should return ProductDetails object.

---

## Actual Result

```text
NoSuchBeanDefinitionException

No bean named 'product' available
```

---

## Why Did This Happen?

Spring never scanned the class.

The class contains:

```java
@Component
```

but Spring was never instructed to:

```text
Search Packages
Find Components
Register Components
```

---

## Internal Flow

```text
Context Created
      │
      ▼
refresh()
      │
      ▼
No Component Scanning
      │
      ▼
ProductDetails Never Found
      │
      ▼
Bean Not Registered
      │
      ▼
getBean("product")
      │
      ▼
Exception
```

---

## Key Observation

`@Component`

alone does NOT create a bean.

Spring must perform:

```text
Component Scanning
```

to discover the class.

Without component scanning:

```java
@Component
```

is completely ignored.

(Next topic will explain Component Scanning and how Spring discovers @Component classes automatically.)
