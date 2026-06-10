# 09. Constructor Injection

## Concept

Constructor Injection is a Dependency Injection technique where Spring supplies all required dependencies through the constructor while creating the object.

Instead of:

```java
AccountDetails acc = new AccountDetails();
acc.setName("Dilip");
acc.setBalance(500);
```

Spring does:

```java
new AccountDetails(
    "Dilip",
    500,
    mobiles,
    address
);
```

---

# Why Constructor Injection?

Imagine an Account object.

Can an account exist without:

- Customer Name
- Balance
- Mobile Numbers
- Address

❌ No

These are mandatory dependencies.

Constructor Injection guarantees that all required data is available at object creation time.

---

# Real-Life Analogy

Consider buying a new mobile phone.

When purchasing:

```text
Mobile Brand
RAM
Storage
Battery
```

must be provided.

The phone cannot be created first and configured later.

Similarly,

```text
Constructor Injection
=
Provide everything while creating object
```

---

# Bean Class

## AccountDetails

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

# Dependency Class

## Address

```java
public class Address {

    private int flatNo;
    private String houseName;
    private long mobile;

}
```

---

# XML Configuration

## Step 1 : Create Address Bean

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

## Step 2 : Inject Dependencies Through Constructor

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

# Understanding constructor-arg

## value Attribute

Used for:

```text
Primitive Types
String Types
```

Example:

```xml
<constructor-arg
        name="name"
        value="Dilip"/>
```

Spring internally:

```java
"Dilip"
```

---

## ref Attribute

Used for:

```text
Object Dependencies
```

Example:

```xml
<constructor-arg
        name="customerAddress"
        ref="addr"/>
```

Spring internally:

```java
Address address = context.getBean("addr");
```

---

# Internal Execution

Spring performs:

```java
Address addr = new Address();

addr.setFlatNo(333);
addr.setHouseName("Lotus Homes");
addr.setMobile(91822222);
```

Then:

```java
Set<String> mobiles = new HashSet<>();

mobiles.add("8826111377");
mobiles.add("8826111377");
mobiles.add("+91-88888888");
mobiles.add("+232388888888");
```

Finally:

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

# Bean Retrieval

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

# Output

```text
Dilip
500.0
[8826111377, +91-88888888, +232388888888]
333
Lotus Homes
```

---

# What Happened Here?

## Duplicate Removed

Input:

```text
8826111377
8826111377
```

Output:

```text
8826111377
```

Reason:

```text
Set does not allow duplicates
```

---

## Address Injected

Spring did:

```java
details.setCustomerAddress(addr);
```

conceptually through constructor.

Result:

```java
details.getCustomerAddress()
```

returns Address object.

---

# Visual Flow

```text
XML
 │
 │
 ▼
Address Bean Created
 │
 ▼
Collection Created
 │
 ▼
Constructor Called
 │
 ▼
AccountDetails Object Created
 │
 ▼
Bean Stored In Container
 │
 ▼
getBean()
 │
 ▼
Object Returned
```

---

# Constructor Injection Rules

## Rule 1

Constructor must exist.

```java
public AccountDetails(
        String name,
        double balance,
        Set<String> mobiles,
        Address customerAddress)
```

Without matching constructor:

```text
BeanCreationException
```

---

## Rule 2

Parameter count must match.

Wrong:

```xml
2 arguments
```

Constructor:

```java
4 parameters
```

Result:

```text
BeanCreationException
```

---

## Rule 3

Data types must match.

Wrong:

```xml
<constructor-arg
        name="balance"
        value="ABC"/>
```

Expected:

```java
double
```

Result:

```text
TypeMismatchException
```

---

# Why Constructor Injection Is Preferred?

Because object becomes complete immediately.

Example:

```java
AccountDetails acc =
        new AccountDetails(
                name,
                balance,
                mobiles,
                address);
```

At this moment:

```text
Name Available
Balance Available
Mobiles Available
Address Available
```

No dependency is missing.

---

# Setter vs Constructor Injection

| Feature | Setter Injection | Constructor Injection |
|----------|----------|----------|
| Injection Method | Setter Methods | Constructor |
| Dependency Required | Optional | Mandatory |
| Object Can Exist Without Dependency | Yes | No |
| Injection Time | After Creation | During Creation |
| Immutability | Less | More |
| Readability | Medium | High |
| Unit Testing | Good | Excellent |
| Recommended For | Optional Data | Mandatory Data |

---

# Interview Trap

### Question

Why is Constructor Injection preferred in Spring?

### Answer

Because:

```text
1. Mandatory dependencies are guaranteed.

2. Object is fully initialized.

3. Better readability.

4. Easier unit testing.

5. Prevents NullPointerException caused by missing dependencies.
```

---

# Common Mistakes

## Mistake 1

Wrong Bean Id

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

## Mistake 2

Missing Constructor

```java
public AccountDetails()
{
}
```

only default constructor exists.

Spring cannot find matching constructor.

Result:

```text
BeanCreationException
```

---

## Mistake 3

Wrong Parameter Order
(when name attribute is not used)

Example:

```xml
<constructor-arg value="500"/>
<constructor-arg value="Dilip"/>
```

Result:

```text
Type mismatch
Wrong values injected
```

---

# Assessment Answer Template

When asked:

### Explain Constructor Injection.

Write:

```text
Constructor Injection is a Dependency Injection technique in which Spring injects dependencies through a class constructor while creating the object.

It is mainly used for mandatory dependencies because the object cannot be created unless all required dependencies are supplied.

Spring uses the <constructor-arg> tag to perform Constructor Injection.

Primitive values are injected using value attribute and object references are injected using ref attribute.

Advantages:
- Mandatory dependencies guaranteed
- Better readability
- Easier unit testing
- Reduces NullPointerException risks
```

---

# Quick Revision

## Injection Tag

```xml
<constructor-arg>
```

---

## Primitive/String

```xml
value=""
```

---

## Object Dependency

```xml
ref=""
```

---

## Collection Injection

```xml
<set>
<list>
<map>
```

---

## Remember

```text
Setter Injection
=
Create Object First
Inject Later
```

```text
Constructor Injection
=
Inject First
Create Object Once
```

---

# One-Line Memory Trick

```text
Setter Injection → Optional Dependencies

Constructor Injection → Mandatory Dependencies
```
