# 09. Constructor Injection

## Read First

Consider an Account.

To create an Account, we need:

- Name
- Balance
- Mobile Numbers
- Address

Question:

Can an Account be created without these?

```text
No
```

So all required data must be provided while creating the object.

This is where Constructor Injection is used.

---

## Apply Concept

Without Constructor Injection:

```java
AccountDetails acc =
        new AccountDetails();

acc.setName("Dilip");

acc.setBalance(500);

acc.setMobiles(mobiles);

acc.setCustomerAddress(address);
```

Object is created first.

Data is supplied later.

---

With Constructor Injection:

```java
new AccountDetails(
        "Dilip",
        500,
        mobiles,
        address
);
```

Everything is supplied during object creation.

---

## Bean Class

```java
public class AccountDetails {

    private String name;

    private double balance;

    private Set<String> mobiles;

    private Address customerAddress;

    public AccountDetails(
            String name,
            double balance,
            Set<String> mobiles,
            Address customerAddress) {

        this.name = name;
        this.balance = balance;
        this.mobiles = mobiles;
        this.customerAddress = customerAddress;
    }
}
```

---

## Dependency Class

```java
public class Address {

    private int flatNo;

    private String houseName;

    private long mobile;

}
```

---

## Structure Clearly

Dependency Flow:

```text
AccountDetails
        │
        ▼
Address
```

AccountDetails needs Address.

Spring will supply Address while creating AccountDetails.

---

## XML Configuration

### Step 1 : Create Address Bean

```xml
<bean id="addr"
      class="com.naresh.hello.Address">

    <property name="flatNo"
              value="333"/>

    <property name="houseName"
              value="Lotus Homes"/>

    <property name="mobile"
              value="91822222"/>

</bean>
```

---

### Step 2 : Create AccountDetails Bean

```xml
<bean id="accountDeatils"
      class="com.naresh.hello.AccountDetails">

    <constructor-arg
            name="name"
            value="Dilip"/>

    <constructor-arg
            name="balance"
            value="500.00"/>

    <constructor-arg name="mobiles">

        <set>

            <value>8826111377</value>

            <value>8826111377</value>

            <value>+91-88888888</value>

            <value>+232388888888</value>

        </set>

    </constructor-arg>

    <constructor-arg
            name="customerAddress"
            ref="addr"/>

</bean>
```

---

## Understanding constructor-arg

### value

Used for:

```text
Primitive Values

String Values
```

Example:

```xml
<constructor-arg
        name="name"
        value="Dilip"/>
```

---

### ref

Used for:

```text
Bean Objects
```

Example:

```xml
<constructor-arg
        name="customerAddress"
        ref="addr"/>
```

Meaning:

```text
Use the Address bean
while creating AccountDetails.
```

---

## What Spring Does

### Step 1

Create Address Bean

```java
Address addr =
        new Address();
```

---

### Step 2

Create Mobiles Collection

```java
Set<String> mobiles =
        new HashSet<>();
```

---

### Step 3

Call Constructor

```java
AccountDetails details =
        new AccountDetails(
                "Dilip",
                500.00,
                mobiles,
                addr
        );
```

---

### Result

```text
AccountDetails Created

Name Available

Balance Available

Mobiles Available

Address Available
```

Everything is ready immediately.

---

## Testing

```java
ApplicationContext context =
        new FileSystemXmlApplicationContext(
                "beans.xml");

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

Lotus Homes
```

---

## Apply Concept

Use Constructor Injection when data is required.

Examples:

```text
Account
→ Name Required

Employee
→ Employee Id Required

Order
→ Customer Required

Bank Account
→ Address Required
```

If object cannot work without the dependency,

use Constructor Injection.

---

## Avoid Buzzwords

Instead of saying:

```text
Constructor Injection is a Dependency Injection technique
for mandatory dependencies.
```

Say:

```text
Spring provides all required data
while creating the object.
```

Simple and correct.

---

## Quick Revision

Constructor Injection:

```text
Create Object
+
Supply Data
=
Same Time
```

XML Tag:

```xml
<constructor-arg>
```

Primitive/String:

```xml
value=""
```

Object:

```xml
ref=""
```

Flow:

```text
Spring Creates Dependencies
            │
            ▼
Spring Calls Constructor
            │
            ▼
Object Created
```

---

## Assessment Answer

```text
Constructor Injection is a way of providing required data and dependent objects through a constructor while creating the bean.

Spring uses the <constructor-arg> tag for Constructor Injection.

Primitive and String values are injected using value attribute and object dependencies are injected using ref attribute.

Constructor Injection is mainly used when all dependencies are required during object creation.
```
