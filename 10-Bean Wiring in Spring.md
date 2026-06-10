# 10. Bean Wiring in Spring

## Problem

Consider the following classes:

```text
AccountDetails
      │
      ▼
Address
      │
      ▼
AreaDetails
```

Now the question is:

```text
Who creates these objects?

Who connects them together?

Who injects dependencies?
```

Without Spring:

```java
AreaDetails area = new AreaDetails();

Address addr = new Address();
addr.setArea(area);

AccountDetails acc =
        new AccountDetails();

acc.setCustomerAddress(addr);
```

Developer manually creates and connects every object.

For large applications:

```text
100 Beans
200 Dependencies
500 Object Connections
```

Management becomes difficult.

---

# Solution

Spring Bean Wiring.

Spring automatically:

```text
Creates Beans

Injects Dependencies

Connects Objects

Stores Them In IOC Container
```

---

# What is Bean Wiring?

## Definition

Bean Wiring is the process of connecting one bean with another bean inside the Spring IOC Container.

Spring establishes relationships between beans using:

```xml
ref=""
```

attribute.

---

# Real World Analogy

Consider:

```text
Customer
    │
    ▼
Address
    │
    ▼
Area
```

A customer lives at an address.

An address belongs to an area.

These objects are connected.

Spring Bean Wiring performs the same connection automatically.

---

# Bean Hierarchy

```text
AccountDetails
       │
       ▼
Address
       │
       ▼
AreaDetails
```

Dependency Chain:

```text
AccountDetails
depends on
Address

Address
depends on
AreaDetails
```

---

# Bean 1 : AreaDetails

## Purpose

Stores area information.

```java
public class AreaDeatils {

    private String street;
    private String pincode;

}
```

---

# Bean 2 : Address

## Purpose

Stores address information.

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

means:

```text
Address depends on AreaDetails
```

---

# Bean 3 : AccountDetails

## Purpose

Stores account information.

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

means:

```text
AccountDetails depends on Address
```

---

# Bean Configuration

## Step 1

Create AreaDetails Bean

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

# Step 2

Create Address Bean

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

# Critical Point

```xml
<property
        name="area"
        ref="areaDetails"/>
```

This is Bean Wiring.

Spring does:

```java
address.setArea(
        areaDetailsBean);
```

automatically.

---

# Step 3

Create AccountDetails Bean

```xml
<bean id="accountDeatils"
      class="com.naresh.hello.AccountDetails">

    <constructor-arg
            name="name"
            value="Dilip"/>

    <constructor-arg
            name="balance"
            value="500"/>

    <constructor-arg
            name="customerAddress"
            ref="addr"/>

    <constructor-arg name="mobiles">

        <set>

            <value>8826111377</value>

            <value>8826111377</value>

            <value>+91-88888888</value>

            <value>+232388888888</value>

        </set>

    </constructor-arg>

</bean>
```

---

# Another Wiring Example

```xml
<constructor-arg
        name="customerAddress"
        ref="addr"/>
```

Spring does:

```java
account.setCustomerAddress(
        addressBean);
```

internally.

---

# Understanding ref Attribute

## value

Used For:

```text
Primitive Types

String Types
```

Example:

```xml
value="Dilip"
```

---

## ref

Used For:

```text
Bean Objects

Dependent Objects

Another Bean
```

Example:

```xml
ref="addr"
```

---

# Internal Spring Execution

## Step 1

Create AreaDetails Bean

```java
AreaDetails area =
        new AreaDetails();

area.setStreet(
        "Naresh It road");

area.setPincode(
        "323232");
```

---

## Step 2

Create Address Bean

```java
Address addr =
        new Address();

addr.setFlatNo(333);

addr.setHouseName(
        "Lotus Homes");

addr.setMobile(
        91822222);
```

---

## Step 3

Wire Area Bean

```java
addr.setArea(area);
```

---

## Step 4

Create AccountDetails Bean

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

# Actual Object Graph

```text
AccountDetails
       │
       ▼
Address
       │
       ▼
AreaDetails
```

Spring creates this graph automatically.

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

# What Happened?

## details.getCustomerAddress()

Returns:

```text
Address Bean
```

---

## details.getCustomerAddress()
   .getArea()

Returns:

```text
AreaDetails Bean
```

---

## details.getCustomerAddress()
   .getArea()
   .getPincode()

Returns:

```text
323232
```

---

# Visual Flow

```text
AreaDetails Bean
       │
       ▼
Address Bean
       │
       ▼
AccountDetails Bean
       │
       ▼
Stored In IOC Container
       │
       ▼
getBean()
       │
       ▼
Object Returned
```

---

# Why Bean Wiring Is Important?

Without Wiring:

```java
new AreaDetails()

new Address()

new AccountDetails()
```

Manual work.

---

With Wiring:

```xml
ref="beanId"
```

Spring handles everything.

---

# Real Project Usage

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

---

## Banking

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

# Common Mistakes

## Wrong Bean Id

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

## Missing Bean

```xml
ref="areaDetails"
```

Bean not created.

Result:

```text
BeanCreationException
```

---

## Using value Instead Of ref

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

# Interview Questions

## What is Bean Wiring?

```text
Connecting one bean with another bean inside Spring IOC Container.
```

---

## Which attribute is used for Bean Wiring?

```xml
ref
```

---

## Which attribute injects primitive values?

```xml
value
```

---

## Difference Between value and ref?

| value | ref |
|---------|---------|
| Primitive | Bean Object |
| String | Dependency |
| Actual Data | Bean Reference |

---

## What does Spring create internally?

```text
Object Graph
```

Example:

```text
AccountDetails
      │
      ▼
Address
      │
      ▼
AreaDetails
```

---

# Exam Killer Lines

### Line 1

```text
Bean Wiring means establishing relationships between Spring Beans.
```

---

### Line 2

```text
Spring uses the ref attribute to connect one bean with another bean.
```

---

### Line 3

```text
Bean Wiring removes manual object creation and dependency management.
```

---

# Quick Revision

## Bean Wiring

```text
Connecting Beans
```

---

## Attribute Used

```xml
ref
```

---

## Primitive Injection

```xml
value
```

---

## Object Injection

```xml
ref
```

---

## Dependency Chain

```text
AccountDetails
      ↓
Address
      ↓
AreaDetails
```

---

# Memory Trick

```text
value
=
Actual Value

ref
=
Reference To Another Bean
```

```text
Bean Wiring

Connect Bean A
        ↓
Connect Bean B
        ↓
Connect Bean C
```

---

# One-Line Exam Answer

```text
Bean Wiring is the process of connecting Spring beans together using the ref attribute so that dependencies are automatically injected and managed by the IOC Container.
```
