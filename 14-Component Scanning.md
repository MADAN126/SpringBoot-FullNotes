# Component Scanning (`@ComponentScan`)

## Situation

We already created classes using `@Component`.

```java
@Component
public class UserDetails {
}
```

Spring knows:

> "This class can become a bean."

But Spring still has one problem.

---

## Problem

How will Spring find this class?

Spring Container cannot scan your entire project automatically.

Imagine a project with:

```text
com.amazon.users
com.amazon.products
com.amazon.orders
com.amazon.payments
```

Spring needs to know:

- Which packages should be searched?
- Which classes contain components?
- Which classes should become beans?

Without this information:

```java
@Component
public class UserDetails {
}
```

is just a normal Java class.

No bean will be created.

---

## Solution

Tell Spring where to search.

This is the purpose of:

```java
@ComponentScan
```

---

## What `@ComponentScan` Actually Does

Think of it as:

```text
Spring Container
        │
        ▼
Search These Packages
        │
        ▼
Find Classes Having
@Component
@Service
@Repository
@Controller
@Configuration
        │
        ▼
Create Bean Objects
        │
        ▼
Store Inside ApplicationContext
```

---

# Why `@Component` Alone Is Not Enough

## Situation

```java
@Component
public class UserDetails {
}
```

---

## Problem

Spring knows this class is a component.

But Spring does not know:

```text
Where is this class?
```

Searching the whole project continuously would be expensive.

---

## Solution

Specify the package.

```java
@Configuration
@ComponentScan("com.amazon")
public class BeansConfiguration {
}
```

Now Spring knows where to search.

---

## Internal Flow

```text
Application Starts
        │
        ▼
BeansConfiguration Loaded
        │
        ▼
@ComponentScan Found
        │
        ▼
Scan Package: com.amazon
        │
        ▼
Find Component Classes
        │
        ▼
Create Bean Objects
        │
        ▼
Store In ApplicationContext
```

---

## Result

Every discovered component becomes a bean automatically.

No manual registration required.

---

# Configuration Class

## Code

```java
@Configuration
@ComponentScan("com.amazon")
public class BeansConfiguration {
}
```

---

## What Spring Understands

```text
@Configuration
      ↓
This class contains bean configuration.

@ComponentScan
      ↓
Search inside com.amazon package.

Found Components
      ↓
Create Beans
```

---

# Scanning Multiple Packages

## Situation

Components exist in multiple package trees.

```text
com.hello.spring
com.hello.spring.boot
```

---

## Solution

Provide multiple package names.

```java
@ComponentScan(
    basePackages = {
        "com.hello.spring",
        "com.hello.spring.boot"
    }
)
```

### Alternative

If there is only one package and its sub-packages:

```java
@ComponentScan("com.hello.spring")
```

---

## Internal Flow

```text
Spring
 │
 ├── Scan com.hello.spring
 │
 └── Scan com.hello.spring.boot
         │
         ▼
   Find Components
         │
         ▼
   Create Beans
```

---

# Alternative to `@ComponentScan`

Instead of writing:

```java
@ComponentScan("com.amazon")
```

You can scan packages directly using `AnnotationConfigApplicationContext`.

```java
AnnotationConfigApplicationContext context =
        new AnnotationConfigApplicationContext();

context.scan("com.amazon");

context.refresh();
```

---

## Internal Flow

```text
scan("com.amazon")
        │
        ▼
Search Packages
        │
        ▼
Find Components
        │
        ▼
Register Bean Definitions
        │
        ▼
refresh()
        │
        ▼
Create Bean Objects
```

### Important Observation

`scan()` only registers what Spring found.

Actual bean creation happens only after:

```java
context.refresh();
```

Without `refresh()`, no beans are created.

---

# Loading Configuration Into Container

## Situation

Spring cannot start scanning until configuration is loaded.

---

## Code

```java
AnnotationConfigApplicationContext context =
        new AnnotationConfigApplicationContext();

context.register(BeansConfiguration.class);

context.refresh();
```

---

## What Each Statement Does

### Step 1

```java
new AnnotationConfigApplicationContext();
```

Creates an empty Spring container.

```text
Container Created
No Beans Yet
No Scanning Yet
```

---

### Step 2

```java
context.register(BeansConfiguration.class);
```

Registers configuration metadata.

```text
Container
      │
      ▼
BeansConfiguration Registered
```

Still no beans created.

---

### Step 3

```java
context.refresh();
```

This is where actual work begins.

```text
Read Configuration
        │
        ▼
Perform Component Scan
        │
        ▼
Create Beans
        │
        ▼
Store In Container
```

---

## Key Observation

Without:

```java
context.refresh();
```

Spring will not process configuration.

No beans will be created.

---

# Getting a Component Bean

## Component

```java
@Component
public class ProductDetails {
}
```

---

## Code

```java
ProductDetails details =
        (ProductDetails) context.getBean("product");
```

---

## Internal Flow

```text
getBean("product")
        │
        ▼
Search Bean Registry
        │
        ▼
Find Bean Named product
        │
        ▼
Return Existing Instance
```

---

## Output

```text
ProductDetails [pname=null, price=0.0]
```

Bean was successfully created by Spring.

---

# Creating Another Component

## Component

```java
@Component
public class UserDetails {
}
```

---

## Problem

How do we retrieve it?

---

## Solution

Use type-based lookup.

```java
UserDetails user =
        context.getBean(UserDetails.class);
```

---

## Internal Flow

```text
UserDetails.class
        │
        ▼
Search Bean Registry
        │
        ▼
Find Bean Of Type UserDetails
        │
        ▼
Return Bean Instance
```

---

## Output

```text
com.amazon.users.UserDetails@5e17553a
```

---

# Bean Lookup by Name vs Type

| Method | Search Based On |
|----------|----------|
| `getBean("user")` | Bean Name |
| `getBean(UserDetails.class)` | Bean Type |

### By Name

```java
context.getBean("user");
```

Spring searches:

```text
Bean ID = user
```

---

### By Type

```java
context.getBean(UserDetails.class);
```

Spring searches:

```text
Class Type = UserDetails
```

---

## Key Observation

Type lookup is cleaner.

No need to remember bean names.

---

# Default Scope of Component Beans

## Situation

Request the same bean twice.

```java
UserDetails u1 =
        context.getBean(UserDetails.class);

UserDetails u2 =
        context.getBean(UserDetails.class);
```

---

## Output

```text
UserDetails@5e17553a

UserDetails@5e17553a
```

Same address.

Same object.

---

## Why?

Because Spring internally assumes:

```java
@Scope("singleton")
```

even if you don't write it.

---

## Internal Flow

```text
Container Startup
        │
        ▼
Create One UserDetails Object
        │
        ▼
Store In Bean Registry
        │
        ▼
Every getBean()
        │
        ▼
Return Same Object
```

---

## Result

```text
One Bean
Many Requests
Same Object Returned
```

---

# Prototype Scope With Components

## Situation

Need a new object every request.

---

## Solution

```java
@Component
@Scope("prototype")
public class UserDetails {
}
```

---

## Internal Flow

```text
getBean()
    │
    ▼
Create New Object
    │
    ▼
Return Object

getBean()
    │
    ▼
Create Another Object
    │
    ▼
Return Object
```

---

## Output

```text
UserDetails@74f6c5d8

UserDetails@27912e3
```

Different addresses.

Different objects.

---

# Can a `@Component` Class Also Have a `@Bean` Configuration?

## Situation

`UserDetails` is already a component.

```java
@Component
public class UserDetails {
}
```

Can we still create it through a `@Bean` method?

Yes.

---

## Code

```java
@Bean("user")
UserDetails getUserDetails() {
    return new UserDetails();
}
```

---

## Internal Flow

```text
Container Startup
        │
        ▼
Execute @Bean Method
        │
        ▼
new UserDetails()
        │
        ▼
Store Bean With Name user
```

---

## Result

```java
UserDetails user =
    (UserDetails) context.getBean("user");
```

Bean retrieved successfully.

---

# Why Use `@Bean` When `@Component` Already Exists?

Because `@Bean` gives more control.

You can:

- Set default values
- Configure properties
- Execute initialization logic
- Create third-party objects

before handing them to Spring.

---

# Passing Default Values To Component Objects

## Situation

Need object with preloaded data.

Instead of:

```java
new UserDetails()
```

we want:

```text
email = dilip@gmail.com
mobile = 8826111377
```

already present.

---

## Solution

Configure inside `@Bean`.

```java
@Bean("user")
UserDetails getUserDetails() {

    UserDetails user =
            new UserDetails();

    user.setEmailId("dilip@gmail.com");
    user.setMobile(8826111377L);

    return user;
}
```

---

## Internal Flow

```text
Container Startup
        │
        ▼
Execute @Bean Method
        │
        ▼
Create UserDetails
        │
        ▼
Set Email
        │
        ▼
Set Mobile
        │
        ▼
Return Object
        │
        ▼
Store Bean
```

---

## Output

```text
dilip@gmail.com

8826111377
```

---

# Complete Picture

```text
@Configuration
        │
        ▼
@ComponentScan("com.amazon")
        │
        ▼
Spring Scans Packages
        │
        ▼
Finds
@Component
@Service
@Repository
@Controller
@Configuration
        │
        ▼
Creates Bean Objects
        │
        ▼
Stores In ApplicationContext
        │
        ▼
getBean()
        │
        ▼
Bean Returned
```

# Key Observations

- `@Component` marks a class as a Spring-managed candidate.
- `@ComponentScan` tells Spring where to search.
- Without scanning, components are never discovered.
- `refresh()` triggers actual bean creation.
- Components are singleton by default.
- `@Scope("prototype")` creates a new object per request.
- `getBean(Class)` performs type-based lookup.
- `@Bean` methods can still create objects of component classes.
- `@Bean` is useful when custom initialization or default values are required.
- `ApplicationContext` acts as the registry that stores and supplies bean instances.
