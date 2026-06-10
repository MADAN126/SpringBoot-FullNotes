# 10. Bean Wiring in Spring

## Problem

We already know:

```text
Spring can create objects.
```

But what if:

```text
AccountDetails needs Address

Address needs AreaDetails
```

Who will connect them?

Without Spring:

```java
AreaDeatils area =
        new AreaDeatils();

Address address =
        new Address();

address.setArea(area);

AccountDetails account =
        new AccountDetails();

account.setCustomerAddress(
        address);
```

Imagine doing this for:

```text
100 Classes

200 Dependencies

500 Object Connections
```

Managing objects becomes difficult.

---

# Why Previous Approach Fails?

Manual Object Creation:

```java
new AreaDeatils();

new Address();

new AccountDetails();
```

Problems:

```text
More Code

More Maintenance

More Bugs

More Coupling

Hard To Manage
```

As project size increases:

```text
Object Management
becomes a nightmare.
```

---

# Solution

## Bean Wiring

Spring says:

```text
You tell me

Which Bean depends on which Bean

I will create them

I will connect them

I will manage them
```

---

# Real World Analogy

Think of a House.

```text
Person
   │
   ▼
House
   │
   ▼
Area
```

A person lives in a house.

A house belongs to an area.

Everything is connected.

Bean Wiring creates the same relationship between Java objects.

---

# Core Concept

## What is Bean Wiring?

Bean Wiring is the process of connecting one Spring Bean with another Spring Bean.

Spring establishes these relationships using:

```xml
ref=""
```

attribute.

---

# Dependency Chain

Our Example:

```text
AccountDetails
       │
       ▼
Address
       │
       ▼
AreaDeatils
```

Meaning:

```text
AccountDetails
depends on
Address

Address
depends on
AreaDeatils
```

---

# Bean 1 : AreaDeatils

## Responsibility

Stores Area Information.

```java
public class AreaDeatils {

    private String street;

    private String pincode;

}
```

---

# Bean 2 : Address

## Responsibility

Stores Address Information.

```java
public class Address {

    private int flatNo;

    private String houseName;

    private long mobile;

    private AreaDeatils area;

}
```

Important:

```java
private AreaDeatils area;
```

Immediately tells us:

```text
Address
depends on
AreaDeatils
```

---

# Bean 3 : AccountDetails

## Responsibility

Stores Account Information.

```java
public class AccountDetails {

    private String name;

    private double balance;

    private Set<String> mobiles;

    private Address customerAddress;

}
```

Important:

```java
private Address customerAddress;
```

Meaning:

```text
AccountDetails
depends on
Address
```

---

# Understanding The Big Picture

```text
AccountDetails
       │
       ▼
Address
       │
       ▼
AreaDeatils
```

This entire hierarchy is called:

```text
Object Graph
```

Spring creates it automatically.

---

# Step 1 : Configure Area Bean

```xml
<bean id="areaDetails"
      class="com.naresh.hello.AreaDeatils">

    <property
            name="street"
            value="Naresh It road"/>

    <property
            name="pincode"
            value="323232"/>

</bean>
```

---

# Step 2 : Configure Address Bean

```xml
<bean id="addr"
      class="com.naresh.hello.Address">

    <property
            name="flatNo"
            value="333"/>

    <property
            name="houseName"
            value="Lotus Homes"/>

    <property
            name="mobile"
            value="91822222"/>

    <property
            name="area"
            ref="areaDetails"/>

</bean>
```

---

# MOST IMPORTANT LINE

```xml
<property
        name="area"
        ref="areaDetails"/>
```

This line is Bean Wiring.

Spring understands:

```text
Address Bean
needs
AreaDetails Bean
```

Spring internally performs:

```java
address.setArea(
        areaDetailsBean);
```

---

# Step 3 : Configure AccountDetails Bean

```xml
<bean id="accountDeatils"
      class="com.naresh.hello.AccountDetails">

    <constructor-arg
            name="name"
            value="Dilip"/>

    <constructor-arg
            name="balance"
            value="500.00"/>

    <constructor-arg
            name="customerAddress"
            ref="addr"/>

</bean>
```

---

# Second Wiring

```xml
<constructor-arg
        name="customerAddress"
        ref="addr"/>
```

Spring internally performs:

```java
new AccountDetails(
        name,
        balance,
        mobiles,
        addressBean
);
```

---

# value vs ref

This is a favorite interview question.

| value | ref |
|---------|---------|
| Primitive Data | Bean Object |
| String Data | Dependency |
| Actual Value | Bean Reference |

---

## Example

Primitive:

```xml
value="Dilip"
```

Object:

```xml
ref="addr"
```

---

# Internal Spring Engine

## Spring Creates Area Bean

```java
AreaDeatils area =
        new AreaDeatils();

area.setStreet(
        "Naresh It road");

area.setPincode(
        "323232");
```

---

## Spring Creates Address Bean

```java
Address addr =
        new Address();

addr.setFlatNo(333);

addr.setHouseName(
        "Lotus Homes");
```

---

## Spring Wires Area Bean

```java
addr.setArea(area);
```

---

## Spring Creates Account Bean

```java
AccountDetails account =
        new AccountDetails(
                "Dilip",
                500,
                mobiles,
                addr
        );
```

---

# What Spring Actually Does

Developer writes:

```xml
ref="areaDetails"
```

Spring executes:

```java
address.setArea(
        areaBean);
```

---

Developer writes:

```xml
ref="addr"
```

Spring executes:

```java
account.setCustomerAddress(
        addressBean);
```

---

# Testing

```java
AccountDetails details =
(AccountDetails)
context.getBean(
        "accountDeatils");
```

---

# Output

```text
Dilip

500.0

[8826111377,
 +91-88888888,
 +232388888888]

333

323232
```

---

# Apply The Concept

## Banking System

```text
Account
   │
   ▼
Address
   │
   ▼
Area
```

---

## Employee Management

```text
Employee
    │
    ▼
Department
    │
    ▼
Location
```

---

## E-Commerce

```text
Order
   │
   ▼
Customer
   │
   ▼
Address
```

Bean Wiring is used everywhere.

---

# Connection With Previous Chapters

## Setter Injection

Used to inject:

```text
Values
```

Example:

```xml
value="Dilip"
```

---

## Constructor Injection

Used to inject:

```text
Dependencies
```

during object creation.

---

## Bean Wiring

Used to:

```text
Connect Beans Together
```

using:

```xml
ref=""
```

---

# Common Mistakes

## Mistake 1

Using value instead of ref.

Wrong:

```xml
<property
        name="area"
        value="areaDetails"/>
```

Correct:

```xml
<property
        name="area"
        ref="areaDetails"/>
```

---

## Mistake 2

Wrong Bean Id.

```xml
ref="address"
```

Actual Bean:

```xml
id="addr"
```

Result:

```text
NoSuchBeanDefinitionException
```

---

## Mistake 3

Dependency Bean Missing.

```xml
ref="areaDetails"
```

but bean not configured.

Result:

```text
BeanCreationException
```

---

# Interview Questions

## What is Bean Wiring?

```text
Connecting one Spring Bean
with another Spring Bean.
```

---

## Which attribute performs Bean Wiring?

```xml
ref
```

---

## Difference Between value and ref?

```text
value → Actual Data

ref → Bean Reference
```

---

## What is Object Graph?

```text
A network of connected objects.
```

Example:

```text
AccountDetails
      │
      ▼
Address
      │
      ▼
AreaDeatils
```

---

# Exam Killer Lines

### Killer Line 1

```text
Bean Wiring establishes relationships between Spring Beans.
```

### Killer Line 2

```text
Spring uses the ref attribute to connect dependent beans.
```

### Killer Line 3

```text
Bean Wiring eliminates manual object creation and dependency management.
```

---

# 30-Second Revision

```text
Problem

Beans need other Beans.

Solution

Bean Wiring.

Attribute Used

ref

Purpose

Connect Beans Together.

value

Primitive Data

ref

Bean Object
```

---

# Memory Trick

```text
value

Value Injection
```

```text
ref

Reference Injection
```

```text
Setter Injection
        ↓
Inject Values

Constructor Injection
        ↓
Inject Dependencies

Bean Wiring
        ↓
Connect Dependencies
```

---

# One-Line Exam Answer

```text
Bean Wiring is the process of connecting Spring beans using the ref attribute so that Spring can automatically manage and inject dependencies between objects.
```
