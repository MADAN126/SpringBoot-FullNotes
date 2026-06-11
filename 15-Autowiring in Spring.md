# Autowiring in Spring (Deep Internal Understanding)

---

# 1. Situation (Why Autowiring Exists)

You are creating objects manually:

```java
Account acc = new Account();
Address addr = new Address();
acc.setAddr(addr);
```

This creates a dependency chain:

```text
Account → depends on → Address
```

Now imagine a real system:

- 50+ services
- 100+ dependencies
- deep object graphs

Manual wiring becomes:

- repetitive
- error-prone
- tightly coupled

---

# 2. Problem (What Breaks Without Autowiring)

Without Spring:

- You manually create every object
- You manually connect dependencies
- You must control creation order

### Core issue:

```text
Account cannot work without Address
BUT Address is created separately
```

So object creation becomes:

```text
manual assembly problem
```

---

# 3. Solution (Spring Autowiring Idea)

Spring says:

> “I already have all objects in the container. I will connect them automatically.”

This is:

```text
Autowiring = Automatic Dependency Injection
```

---

# 4. Important Rule

Autowiring works ONLY with:

✔ Object references  
❌ primitives  
❌ String values  

```java
@Autowired
private Address address;   // OK

private String name;       // NOT autowired
```

---

# 5. Autowiring Modes (Legacy XML Concept)

| Mode | Meaning | Internal Injection |
|------|--------|--------------------|
| no | no autowiring | manual |
| byName | match bean name | setter |
| byType | match class type | setter |
| constructor | match constructor args | constructor |

---

# 6. byName Autowiring

## Situation
Inject based on NAME matching.

---

## Rule

```text
field name == bean id
```

---

## XML

```xml
<bean id="address" class="com.demo.Address"/>

<bean id="employee" class="com.demo.Employee" autowire="byName"/>
```

---

## Internal Flow

```text
field: address
      ↓
search bean id "address"
      ↓
match found
      ↓
call setAddress()
      ↓
inject dependency
```

---

## Key Point

✔ Uses setter injection  
✔ Very strict naming

---

# 7. byType Autowiring

## Situation
Inject based ONLY on CLASS TYPE.

---

## Rule

```text
match by data type
```

---

## XML

```xml
<bean id="address" class="com.demo.Address"/>
<bean id="employee" class="com.demo.Employee" autowire="byType"/>
```

---

## Internal Flow

```text
Employee needs Address
        ↓
Spring searches by TYPE = Address
        ↓
Find 1 bean
        ↓
Inject
```

---

## Key Point

✔ Field name ignored  
✔ Only type matters  
✔ Uses setter injection

---

## Failure Case

```text
2 beans of same type → ERROR
```

---

# 8. constructor Autowiring

## Situation
Dependencies must be provided during object creation.

---

## Rule

```text
inject via constructor
```

---

## XML

```xml
<bean id="address" class="com.demo.Address"/>
<bean id="department" class="com.demo.Department"/>

<bean id="employee" class="com.demo.Employee" autowire="constructor"/>
```

---

## Java

```java
public Employee(Address address, Department department) {
    this.address = address;
    this.department = department;
}
```

---

## Internal Flow

```text
Spring sees constructor
        ↓
Find parameters (Address, Department)
        ↓
Resolve beans
        ↓
Call constructor
        ↓
Object created fully initialized
```

---

## Key Point

✔ Object is NEVER created partially  
✔ Dependencies are mandatory  
✔ Best design approach

---

## Failure Case

```text
Multiple beans of same type → ambiguity error
```

---

# 9. Modern @Autowired (Spring Boot Style)

Spring Boot replaces XML modes with:

```java
@Autowired
```

---

# 10. Field Injection

```java
@Component
public class Account {

    @Autowired
    private Address address;
}
```

---

## Internal Flow

```text
Create Account object
        ↓
Find @Autowired field
        ↓
Resolve byType
        ↓
Inject Address
        ↓
Bean ready
```

---

# 11. Multiple Beans Problem (REAL ISSUE)

```java
@Component("home")
class Address {}

@Bean("office")
class Address {}
```

Injection:

```java
@Autowired
private Address address;
```

---

## Problem Flow

```text
byType → find Address
        ↓
2 beans found
        ↓
CONFUSION
        ↓
Exception
```

---

## Error

```text
NoUniqueBeanDefinitionException
```

---

# 12. Solution: @Qualifier

```java
@Autowired
@Qualifier("home")
private Address address;
```

---

## Flow

```text
byType → multiple found
        ↓
Qualifier filters "home"
        ↓
inject correct bean
```

---

# 13. Full Autowiring Internal Flow

```text
@ComponentScan
        ↓
Spring finds beans
        ↓
ApplicationContext stores them
        ↓
@Bean + @Component processed
        ↓
Object graph created
        ↓
@Autowired resolves dependencies
        ↓
Injection completed
```

---

# 14. Key Observations

- Autowiring = automatic dependency resolution
- Works only for objects
- byType is default logic in annotations
- byName uses field-name matching (XML)
- constructor ensures fully initialized objects
- multiple beans → ambiguity error
- @Qualifier resolves conflicts
- Spring always builds full object graph

---

# Final Mental Model

```text
1. Spring creates all beans
2. Builds dependency graph
3. Matches dependencies (byType/byName/constructor)
4. Injects them
5. Gives fully ready object
```

---

# One-line understanding

👉 Autowiring = Spring automatically connecting beans by resolving dependencies using type, name, or constructor while building the application context.
