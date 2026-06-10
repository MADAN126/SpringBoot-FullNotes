# Bean Configuration Using Java-Based Configuration

## Situation

In XML-based configuration, bean information is stored inside `beans.xml`.

```xml
<bean id="student" class="com.dilip.beans.Student">
    <property name="name" value="Dilip Singh"/>
    <property name="studentID" value="1111"/>
    <property name="mobile" value="8826111377"/>
</bean>
```

Spring reads XML, creates objects, injects values, and stores them inside the IoC Container.

---

## Problem

As applications become larger:

- XML files become huge.
- Bean configurations increase.
- Managing configurations becomes difficult.
- Configuration remains separate from Java code.

Developers wanted a way to configure beans directly inside Java.

---

## Solution

Spring introduced **Java-Based Configuration**.

Instead of writing bean definitions inside XML, bean definitions can be written inside Java classes using:

- `@Configuration`
- `@Bean`

---

# Understanding @Configuration

## Situation

Spring needs a place where bean creation instructions are stored.

---

## Problem

Without XML, Spring does not know where bean definitions exist.

---

## Solution

Mark a class with `@Configuration`.

```java
@Configuration
public class BeansConfiguration {

}
```

---

## What Spring Understands

When Spring sees:

```java
@Configuration
```

it understands:

> "This class contains bean definitions."

---

## Internal Flow

```text
Application Starts
        │
        ▼
Spring Finds @Configuration Class
        │
        ▼
Configuration Metadata Created
        │
        ▼
@Bean Methods Searched
        │
        ▼
Bean Definitions Registered
```

---

## Key Observation

`@Configuration`

does NOT create beans.

It only tells Spring where bean definitions are located.

---

# Understanding @Bean

## Situation

We need Spring to create a `UserDetails` object.

```java
public class UserDetails {

    private String firstName;
    private String lastName;
    private String emailId;
    private String password;
    private long mobile;

}
```

---

## Problem

Spring still needs instructions about:

- What object to create
- What bean name to assign

---

## Solution

Create a bean method.

```java
@Configuration
public class BeansConfiguration {

    @Bean("userDetails")
    UserDetails getUserDetails() {
        return new UserDetails();
    }

}
```

---

## What Spring Understands

Spring sees:

```java
@Bean("userDetails")
```

and understands:

```text
Bean Name = userDetails
Bean Type = UserDetails
```

The returned object becomes the bean.

```java
return new UserDetails();
```

---

## XML Equivalent

```xml
<bean id="userDetails"
      class="com.amazon.users.UserDetails"/>
```

---

## Internal Flow

```text
@Configuration Class
        │
        ▼
@Bean Method Found
        │
        ▼
Method Executed
        │
        ▼
new UserDetails()
        │
        ▼
Object Returned
        │
        ▼
Stored In IoC Container
        │
        ▼
Bean Ready
```

---

## Key Observation

The object returned from the method becomes a Spring Bean.

---

# Loading Java-Based Configuration

## Code

```java
AnnotationConfigApplicationContext context =
        new AnnotationConfigApplicationContext();

context.register(BeansConfiguration.class);

context.refresh();

UserDetails user =
        (UserDetails) context.getBean("userDetails");

System.out.println(user);

context.close();
```

---

# Understanding AnnotationConfigApplicationContext

## Situation

XML configuration uses:

```java
ClassPathXmlApplicationContext
```

Java configuration needs a container that understands annotations.

---

## Solution

Use:

```java
AnnotationConfigApplicationContext
```

---

## Internal Flow

```text
Create Context
      │
      ▼
Register Configuration Class
      │
      ▼
Read @Configuration
      │
      ▼
Read @Bean Methods
      │
      ▼
Create Bean Definitions
      │
      ▼
Create Bean Objects
      │
      ▼
Store In Container
      │
      ▼
Ready For Usage
```

---

# Understanding register()

## Situation

Spring must know which configuration classes to process.

---

## Solution

```java
context.register(BeansConfiguration.class);
```

---

## What Happens Internally?

Spring stores:

```java
BeansConfiguration.class
```

for later processing.

No beans are created yet.

---

## Flow

```text
register()
      │
      ▼
Configuration Registered
      │
      ▼
Waiting For refresh()
```

---

## Key Observation

Registering is NOT bean creation.

---

# Understanding refresh()

## Situation

Configuration classes are registered.

Beans still do not exist.

---

## Solution

```java
context.refresh();
```

---

## What Happens Internally?

Spring:

1. Reads configuration classes.
2. Finds `@Bean` methods.
3. Creates bean definitions.
4. Creates objects.
5. Stores objects in the container.

---

## Flow

```text
Registered Configuration
            │
            ▼
Read Metadata
            │
            ▼
Find @Bean Methods
            │
            ▼
Create Bean Definitions
            │
            ▼
Create Bean Objects
            │
            ▼
Store In IoC Container
```

---

## Key Observation

`refresh()` is the actual trigger that creates beans.

Without it:

```java
context.getBean(...)
```

will fail.

---

# Understanding getBean()

## Situation

Bean already exists inside the container.

---

## Solution

```java
UserDetails user =
        (UserDetails) context.getBean("userDetails");
```

---

## Internal Flow

```text
Search Bean Name
        │
        ▼
Locate Bean Object
        │
        ▼
Return Reference
        │
        ▼
Assign To Variable
```

---

# Understanding close()

## Situation

Application work is completed.

---

## Solution

```java
context.close();
```

---

## Internal Flow

```text
Container Closing
        │
        ▼
Destroy Beans
        │
        ▼
Release Resources
        │
        ▼
Shutdown Complete
```

---

# Multiple Configuration Classes

## Situation

Application contains multiple bean types.

Example:

```java
UserDetails
```

```java
ProductDetails
```

---

## Problem

Keeping all bean configurations inside one class becomes difficult.

---

## Solution

Create multiple configuration classes.

```java
context.register(BeansConfiguration.class);

context.register(BeansConfigurationTwo.class);
```

---

## Internal Flow

```text
Configuration 1 Registered
            │
            ▼
Configuration 2 Registered
            │
            ▼
refresh()
            │
            ▼
Read Both Configurations
            │
            ▼
Create All Bean Definitions
            │
            ▼
Create All Bean Objects
            │
            ▼
Store In Container
```

---

## Result

Both beans become available.

```java
context.getBean("userDetails");

context.getBean("productDetails");
```

---

# Multiple Beans of Same Class

## Situation

Sometimes multiple objects of the same class are required.

Example:

Two ProductDetails objects.

---

## Solution

Create multiple bean definitions.

```java
@Bean("productDetails")
ProductDetails productDetails() {
    return new ProductDetails();
}

@Bean("productDetailsTwo")
ProductDetails productTwoDetails() {
    return new ProductDetails();
}
```

---

## Internal Flow

```text
@Bean Method 1
        │
        ▼
new ProductDetails()
        │
        ▼
Object #1 Stored
        │
        ▼
@Bean Method 2
        │
        ▼
new ProductDetails()
        │
        ▼
Object #2 Stored
```

---

## Container View

| Bean Name | Object |
|------------|---------|
| productDetails | ProductDetails Object #1 |
| productDetailsTwo | ProductDetails Object #2 |

---

## Result

```text
com.amazon.products.ProductDetails@7dfb0c0f

com.amazon.products.ProductDetails@626abbd0
```

Different memory references indicate different objects.

---

## Key Observation

Same Class

≠

Same Bean

Each `@Bean` method creates a separate bean definition.

```java
@Bean("productDetails")
```

and

```java
@Bean("productDetailsTwo")
```

create two independent Spring-managed objects.

---

# Relationship Between @Configuration and @Bean

| Annotation | Responsibility |
|------------|---------------|
| @Configuration | Declares configuration source |
| @Bean | Declares bean creation logic |

---

## Internal Relationship

```text
@Configuration Class
          │
          ▼
Contains @Bean Methods
          │
          ▼
@Bean Methods Execute
          │
          ▼
Objects Returned
          │
          ▼
Spring Registers Objects
          │
          ▼
Objects Become Beans
```

---

# Complete Bean Creation Flow

```text
POJO Class
      │
      ▼
@Configuration Class
      │
      ▼
@Bean Method
      │
      ▼
register()
      │
      ▼
refresh()
      │
      ▼
Spring Reads Configuration
      │
      ▼
Bean Definition Created
      │
      ▼
Object Created
      │
      ▼
Stored In IoC Container
      │
      ▼
getBean()
      │
      ▼
Bean Returned To Application
```
