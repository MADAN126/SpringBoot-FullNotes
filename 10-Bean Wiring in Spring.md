# 10. Bean Wiring in Spring

## Situation

We have multiple objects.

```text
AccountDetails
       │
       ▼
Address
       │
       ▼
AreaDetails
```

Questions:

```text
How does AccountDetails get Address?

How does Address get AreaDetails?

Who connects these objects?
```

Without Spring, the developer must manually create and connect every object.

```java
AreaDeatils area = new AreaDeatils();

Address addr = new Address();
addr.setArea(area);

AccountDetails account =
        new AccountDetails();

account.setCustomerAddress(addr);
```

As dependencies increase:

```text
More Objects
      ↓
More Connections
      ↓
More Manual Work
```

---

## Solution : Bean Wiring

Spring can create the objects and connect them automatically.

```text
Bean Wiring
      ↓
Connecting One Bean
With Another Bean
```

Instead of:

```text
Developer Connects Objects
```

Spring performs:

```text
Spring Creates Objects
         +
Spring Connects Objects
```

---

# Understanding The Dependency Chain

## AreaDetails

Stores area information.

```java
public class AreaDeatils {

    private String street;

    private String pincode;

}
```

Example:

```text
Street  → Naresh IT Road

Pincode → 323232
```

---

## Address

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

Meaning:

```text
Address
    ↓
Needs
    ↓
AreaDetails
```

---

## AccountDetails

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

Meaning:

```text
AccountDetails
       ↓
Needs
       ↓
Address
```

---

# Complete Dependency Structure

```text
AccountDetails
       │
       ▼
Address
       │
       ▼
AreaDetails
```

Spring must create all three objects and connect them correctly.

---

# Step 1 : Create AreaDetails Bean

```xml
<bean id="areaDetails"
      class="com.naresh.hello.AreaDeatils">

    <property name="street"
              value="Naresh It road"/>

    <property name="pincode"
              value="323232"/>

</bean>
```

## What Spring Does

```java
AreaDeatils area =
        new AreaDeatils();

area.setStreet(
        "Naresh It road");

area.setPincode(
        "323232");
```

Result:

```text
AreaDetails Bean Ready
```

---

# Step 2 : Create Address Bean

```xml
<bean id="addr"
      class="com.naresh.hello.Address">

    <property name="flatNo"
              value="333"/>

    <property name="houseName"
              value="Lotus Homes"/>

    <property name="mobile"
              value="91822222"/>

    <property name="area"
              ref="areaDetails"/>

</bean>
```

Important Line:

```xml
<property
        name="area"
        ref="areaDetails"/>
```

Meaning:

```text
Inject The AreaDetails Bean
Into Address Bean
```

## What Spring Does

```java
Address addr =
        new Address();

addr.setFlatNo(333);

addr.setHouseName(
        "Lotus Homes");

addr.setMobile(
        91822222);

addr.setArea(area);
```

Result:

```text
Address
    │
    ▼
AreaDetails
```

Connection established.

---

# Step 3 : Create AccountDetails Bean

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

Important Line:

```xml
<constructor-arg
        name="customerAddress"
        ref="addr"/>
```

Meaning:

```text
Inject The Address Bean
Into AccountDetails Bean
```

## What Spring Does

```java
AccountDetails account =
        new AccountDetails(
                "Dilip",
                500.00,
                mobiles,
                addr
        );
```

Result:

```text
AccountDetails
       │
       ▼
Address
       │
       ▼
AreaDetails
```

Full dependency chain is ready.

---

# value vs ref

| Attribute | Used For |
|------------|------------|
| value | Primitive Values, String Values |
| ref | Bean Objects, Dependencies |

---

## value Example

```xml
<property
        name="street"
        value="Naresh It road"/>
```

Spring injects:

```java
"Naresh It road"
```

---

## ref Example

```xml
<property
        name="area"
        ref="areaDetails"/>
```

Spring injects:

```java
AreaDeatils area =
        context.getBean(
                "areaDetails");
```

---

# Complete Internal Flow

```text
Spring Starts
      │
      ▼
Reads beans.xml
      │
      ▼
Creates AreaDetails Bean
      │
      ▼
Creates Address Bean
      │
      ▼
Injects AreaDetails
Into Address
      │
      ▼
Creates AccountDetails Bean
      │
      ▼
Injects Address
Into AccountDetails
      │
      ▼
Stores Beans In Container
      │
      ▼
Application Calls getBean()
      │
      ▼
Configured Object Returned
```

---

# Testing

```java
AccountDetails details =
(AccountDetails)
context.getBean(
        "accountDeatils");
```

Spring returns the fully connected object.

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

## Understanding The Output

```text
Dilip
   ↓
Account Name

500.0
   ↓
Account Balance

333
   ↓
Flat Number

323232
   ↓
Area Pincode
```

Notice:

```java
details
    .getCustomerAddress()
    .getArea()
    .getPincode();
```

works because Spring already connected:

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

# Final Understanding

Bean Wiring is simply telling Spring:

```text
This Object Needs That Object
```

Spring then:

```text
Creates Objects
      ↓
Connects Objects
      ↓
Stores Objects
      ↓
Returns Ready-To-Use Objects
```

In this example:

```text
AccountDetails
       │
       ▼
Address
       │
       ▼
AreaDetails
```

The connections are created using:

```xml
ref=""
```

and Spring automatically wires all dependent objects together.
