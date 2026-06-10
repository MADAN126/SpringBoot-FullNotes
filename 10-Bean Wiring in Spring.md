# 10. Bean Wiring in Spring

## Read First

Suppose we have:

```text
AccountDetails
       │
       ▼
Address
       │
       ▼
AreaDetails
```

Question:

```text
How does AccountDetails get Address?

How does Address get AreaDetails?

Who connects these objects?
```

Without Spring:

```java
AreaDeatils area = new AreaDeatils();

Address addr = new Address();
addr.setArea(area);

AccountDetails account =
        new AccountDetails();

account.setCustomerAddress(addr);
```

Developer must manually create and connect every object.

---

## Bean Wiring

Bean Wiring means:

```text
Connecting one bean
with another bean.
```

Spring creates the objects and connects them automatically.

---

## Apply Concept

Real Scenario:

```text
AccountDetails
needs
Address

Address
needs
AreaDetails
```

This dependency chain is:

```text
AccountDetails
       │
       ▼
Address
       │
       ▼
AreaDetails
```

Spring will create all objects and connect them.

---

## Bean 1 : AreaDetails

Stores area information.

```java
public class AreaDeatils {

    private String street;

    private String pincode;

}
```

Example:

```text
Street  : Naresh IT Road
Pincode : 323232
```

---

## Bean 2 : Address

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
Address depends on AreaDetails.
```

---

## Bean 3 : AccountDetails

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
AccountDetails depends on Address.
```

---

## Structure Clearly

Dependency Structure:

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

## XML Configuration

### Step 1 : Create AreaDetails Bean

```xml
<bean id="areaDetails"
      class="com.naresh.hello.AreaDeatils">

    <property name="street"
              value="Naresh It road"/>

    <property name="pincode"
              value="323232"/>

</bean>
```

---

### Step 2 : Create Address Bean

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
Connect Address Bean
with AreaDetails Bean.
```

---

### Step 3 : Create AccountDetails Bean

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
Connect AccountDetails Bean
with Address Bean.
```

---

## What Spring Does Internally

Spring internally performs:

### Step 1

```java
AreaDeatils area =
        new AreaDeatils();
```

### Step 2

```java
Address addr =
        new Address();
```

### Step 3

```java
addr.setArea(area);
```

### Step 4

```java
AccountDetails account =
        new AccountDetails(
                name,
                balance,
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

All objects become connected.

---

## value vs ref

### value

Used for:

```text
Primitive Values

String Values
```

Example:

```xml
value="Dilip"
```

---

### ref

Used for:

```text
Bean Objects

Dependencies
```

Example:

```xml
ref="addr"
```

---

## Testing

```java
AccountDetails details =
(AccountDetails)
context.getBean(
        "accountDeatils");
```

---

## Output

```text
Dilip

500.0

[8826111377,
 +91-88888888,
 +232388888888]

333

323232
```

Explanation:

```text
Dilip      → Account Name

500.0      → Balance

333        → Flat Number

323232     → Area Pincode
```

---

## Avoid Buzzwords

Instead of saying:

```text
Bean Wiring is the process
of configuring relationships
between beans.
```

Say:

```text
Bean Wiring tells Spring
which object should be
connected to which object.
```

Much easier to understand.

---

## Quick Revision

```text
Bean Wiring

=
Connecting Beans
```

```text
value

=
Primitive/String
```

```text
ref

=
Bean Object
```

Dependency Flow:

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

## Assessment Answer

```text
Bean Wiring is the process of connecting one Spring Bean with another Spring Bean. Spring uses the ref attribute to establish relationships between dependent objects and manages them automatically through the IoC Container.
```
