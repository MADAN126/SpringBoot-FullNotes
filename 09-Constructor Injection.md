# 09. Constructor Injection

## Why Constructor Injection?

An Account needs:

- Name
- Balance
- Mobiles
- Address

Without these values, the Account object is incomplete.

Instead of creating the object first and supplying data later:

```java
AccountDetails acc =
        new AccountDetails();

acc.setName("Dilip");
acc.setBalance(500);
```

Spring can supply everything while creating the object:

```java
new AccountDetails(
        "Dilip",
        500,
        mobiles,
        address
);
```

This approach is called Constructor Injection.

---

## AccountDetails Bean

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

Constructor parameters represent the data required to create the object.

---

## Address Dependency

```java
public class Address {

    private int flatNo;

    private String houseName;

    private long mobile;

}
```

AccountDetails depends on Address.

```text
AccountDetails
        │
        ▼
Address
```

---

## XML Configuration

### Address Bean

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

### Constructor Injection

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

## Understanding value and ref

### value

Used for actual values.

```xml
value="Dilip"
```

Spring injects:

```java
"Dilip"
```

---

### ref

Used for another bean.

```xml
ref="addr"
```

Spring injects:

```java
Address addressBean
```

---

## What Spring Does Internally

Spring creates Address:

```java
Address addr =
        new Address();
```

Creates collection:

```java
Set<String> mobiles =
        new HashSet<>();
```

Calls constructor:

```java
AccountDetails details =
        new AccountDetails(
                "Dilip",
                500,
                mobiles,
                addr
        );
```

Object is fully ready immediately after creation.

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

Duplicate mobile number appears once because:

```text
Set removes duplicates.
```

---

## Constructor Injection vs Setter Injection

Setter Injection:

```text
Create Object
      ↓
Inject Data
```

Constructor Injection:

```text
Inject Data
      ↓
Create Object
```

---

## When To Use

Use Constructor Injection when the object cannot work without the dependency.

Examples:

```text
Account → Address Required

Order → Customer Required

Employee → Employee Id Required
```

Required dependency:

```text
Constructor Injection
```

Optional dependency:

```text
Setter Injection
```

---

## Quick Revision

```xml
<constructor-arg>
```

Inject through constructor.

```xml
value=""
```

Primitive/String value.

```xml
ref=""
```

Another bean.

```text
Setter Injection

Create First
Inject Later
```

```text
Constructor Injection

Inject While Creating
```
