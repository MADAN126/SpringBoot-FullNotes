# Creating First Spring Core Application

## Objective

In this chapter, we will create our first Spring Core application and understand how Spring IoC Container creates and manages objects.

---

# Ways to Create a Spring Core Project

Spring Core projects can be created using two approaches:

## 1. Manual JAR Configuration

- Create Java Project
- Download Spring JAR files
- Configure Build Path manually
- Add required Spring libraries

### Limitation

Developers must manually manage all JAR files and dependencies.

---

## 2. Maven Configuration (Recommended)

- Create Java Project
- Convert Project to Maven Project
- Add Spring dependencies in `pom.xml`
- Maven automatically downloads required JAR files

### Benefit

No manual JAR management is required.

---

# Why Maven is Preferred?

Without Maven:

```text
Developer
     ↓
Downloads JAR Files
     ↓
Adds JAR Files Manually
     ↓
Manages Dependencies
```

With Maven:

```text
Developer
     ↓
Adds Dependency in pom.xml
     ↓
Maven Downloads Everything
```

### Benefit

- Faster Setup
- Automatic Dependency Management
- Easier Maintenance

---

# Step 1: Create POJO Class

Create a simple Java class.

```java
package com.naresh.hello;

public class Student {

    private String studentName;

    public String getStudentName() {
        return studentName;
    }

    public void setStudentName(String studentName) {
        this.studentName = studentName;
    }
}
```

---

## Why POJO?

POJO stands for:

> Plain Old Java Object

A POJO contains only business data and business logic.

Spring can manage POJO classes as Beans.

---

# Step 2: Create XML Configuration File

Create:

```text
beans.xml
```

This file contains Spring Bean configurations.

---

# XML Configuration Structure

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

---

# Why beans.xml?

Spring needs a place where Bean definitions are stored.

Think of `beans.xml` as:

```text
Spring Bean Register
```

It tells Spring:

- Which class to create
- What name to assign
- How to manage it

---

# Step 3: Configure Bean

Configure Student class inside `beans.xml`.

```xml
<bean id="stu"
      class="com.naresh.hello.Student">
</bean>
```

---

# Understanding Bean Configuration

## id

```xml
id="stu"
```

Acts like an object reference name.

Used to retrieve the bean from Spring Container.

---

## class

```xml
class="com.naresh.hello.Student"
```

Represents the fully qualified class name.

Format:

```text
packageName.className
```

---

# Step 4: Create Spring Container

Create Main Class.

```java
package com.naresh.hello;

import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.support.FileSystemXmlApplicationContext;

public class SpringCoreApp {

    public static void main(String[] args) {

        BeanFactory factory =
            new FileSystemXmlApplicationContext(
            "D:\\workspaces\\naresit\\hello_spring\\beans.xml");

        Student s =
            (Student) factory.getBean("stu");

        System.out.println(s);
    }
}
```

---

# What Happens Internally?

### Step 1

Spring loads:

```text
beans.xml
```

---

### Step 2

Spring reads:

```xml
<bean id="stu"
      class="Student">
```

---

### Step 3

Spring creates:

```java
new Student();
```

Internally.

---

### Step 4

Object is stored inside:

```text
Spring IoC Container
```

---

### Step 5

Application requests object:

```java
factory.getBean("stu");
```

---

### Step 6

Spring returns the object.

```text
Application
      ↓
getBean("stu")
      ↓
Spring Container
      ↓
Returns Student Object
```

---

# Most Important Observation

Notice:

```java
Student s =
    (Student) factory.getBean("stu");
```

We never wrote:

```java
new Student();
```

### Traditional Java

```java
Student s = new Student();
```

Developer creates object.

---

### Spring

```java
Student s =
   (Student) factory.getBean("stu");
```

Spring creates object.

---

# Why Is This Important?

This is the core idea of:

## Inversion of Control (IoC)

Traditional Java:

```text
Developer Creates Object
```

Spring:

```text
Spring Creates Object
```

Control is transferred from developer to Spring Container.

---

# Creating Multiple Bean Objects

Same class can have multiple bean configurations.

```xml
<bean id="s1"
      class="com.naresh.hello.Student"/>

<bean id="s2"
      class="com.naresh.hello.Student"/>
```

---

# Result

Spring creates:

```text
Student Object 1 → s1

Student Object 2 → s2
```

Both objects belong to the same class but have different bean IDs.

---

# Key Points

- Spring Core projects can be created using Manual JARs or Maven.
- Maven is the preferred approach.
- POJO classes are configured inside `beans.xml`.
- Every configured object becomes a Spring Bean.
- Spring IoC Container creates and manages beans.
- Objects are retrieved using `getBean()`.
- Developers do not need to use `new` operator.
- Multiple beans can be created for the same class.

---

# Conclusion

A Spring Core application works by defining POJO classes as Beans inside `beans.xml`. The Spring IoC Container reads the configuration, creates objects automatically, and provides them whenever requested through `getBean()`. This demonstrates the fundamental concepts of Spring: Bean Management, IoC, and Dependency Injection.
