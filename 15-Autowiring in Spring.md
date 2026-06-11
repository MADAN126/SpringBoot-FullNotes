# Autowiring in Spring (Deep Internal Understanding)

---

# 1. Situation (Why Autowiring Exists)

You are creating objects manually like this:

```java
Account acc = new Account();
Address addr = new Address();
acc.setAddr(addr);
```

This creates a dependency chain:

```text
Account → depends on → Address
```

Now imagine a large system:

- 50 services
- 100 dependencies
- deep object graphs

Manual wiring becomes:

- repetitive
- error-prone
- tightly coupled

---

# 2. Problem (What Breaks Without Autowiring)

Without Spring automation:

- You must manually create every object
- You must manually connect dependencies
- You must remember object creation order

### Real issue:

```text
Account cannot function without Address
BUT Address is created separately
```

So every object creation becomes a "manual assembly problem".

---

# 3. Solution (Spring Autowiring Idea)

Spring says:

> "I already know all objects in the container. Let me connect them automatically."

This is called:

```text
Autowiring = Automatic Dependency Injection
```

---

# 4. Important Rule

Autowiring works ONLY with:

✔ Object references  
❌ NOT primitives  
❌ NOT String values  

Example:

```java
@Autowired
private Address address;   // OK

private String name;       // NOT autowired
```

---

# 5. Autowiring Modes (Old XML Concept)

| Mode | What Spring Uses | Internal Logic |
|------|------------------|----------------|
| no | nothing | manual wiring |
| byName | bean name | setter injection |
| byType | class type | setter injection |
| constructor | constructor params | constructor injection |

---

## 5.1 byName

### Idea

Match property name with bean name.

```text
property name = bean id
```

### Flow

```text
property: address
        ↓
search bean named "address"
        ↓
inject it
```

---

## 5.2 byType

### Idea

Match by class type.

```text
Address addr → inject Address bean
```

### Flow

```text
Find bean of type Address
        ↓
Inject into field
```

---

## 5.3 constructor

### Idea

Spring calls constructor and supplies dependencies.

```text
Create object → pass dependencies inside constructor
```

---

# 6. Modern Spring (@Autowired)

Spring Boot removes manual wiring modes and uses:

```java
@Autowired
```

---

# 7. Field Injection (@Autowired on Property)

## Situation

Account needs Address.

---

## Code

```java
@Component
public class Address {
}
```

```java
@Component
public class Account {

    @Autowired
    private Address addr;
}
```

---

## What Spring Does Internally

```text
Step 1: Scan @Component classes
        ↓
Step 2: Create Address bean
        ↓
Step 3: Create Account bean
        ↓
Step 4: Detect @Autowired field
        ↓
Step 5: Inject Address into Account
```

---

## Flow Diagram

```text
@ComponentScan
      ↓
Address Bean Created
      ↓
Account Bean Created
      ↓
@Autowired Found
      ↓
Dependency Injected
      ↓
Account Ready
```

---

## Result

```text
Address object automatically inside Account
```

---

## Key Observation

Spring is doing:

```text
new Account() → then auto-filling Address inside it
```

You never call `new Address()` manually.

---

# 8. Testing Injection

```java
Account account = context.getBean(Account.class);

Address address = account.getAddr();
address.setPincode(500072);

System.out.println(address);
```

---

## Output Meaning

```text
Address [streetName=null, pincode=500072]
```

✔ Address was injected  
✔ Not manually created  
✔ Spring controlled lifecycle  

---

# 9. Multiple Beans Problem (REAL AUTOWIRING FAILURE)

## Situation

Two beans of same type exist:

```java
@Component("home")
public class Addresss { }

@Bean("hyd")
Addresss createAddress() { }
```

Now Spring container has:

```text
home → Addresss
hyd  → Addresss
```

---

## Injection Point

```java
@Autowired
private Addresss add;
```

---

# 10. Problem (Ambiguity)

Spring tries:

```text
Find Addresss type bean
        ↓
Found TWO:
   - home
   - hyd
        ↓
ERROR ❌
```

---

## Exception Meaning

```text
expected single bean but found 2
```

Spring says:

> "I don't know which one to inject."

---

## Internal Flow Failure

```text
@Autowired
        ↓
Search by Type: Addresss
        ↓
Match 2 Beans
        ↓
Confusion
        ↓
Throw Exception
```

---

# 11. Solution: @Qualifier

## Idea

Tell Spring EXACT bean name to inject.

---

## Fix

```java
@Autowired
@Qualifier("hyd")
private Addresss add;
```

---

## Flow After Fix

```text
@Autowired
        ↓
Find Addresss type
        ↓
Filter by name = "hyd"
        ↓
Inject correct bean
```

---

## Result

```text
Employee gets ONLY hyd bean
```

---

# 12. Final Understanding (Core Mental Model)

## Spring Autowiring is NOT magic

It is:

```text
1. Scan classes
2. Create beans
3. Store in container
4. Match dependencies
5. Inject automatically
```

---

# 13. Full Internal Flow

```text
@ComponentScan
        ↓
Spring detects beans
        ↓
ApplicationContext stores them
        ↓
@Bean + @Component processed
        ↓
Object graph built
        ↓
@Autowired resolves dependencies
        ↓
Injection completed
        ↓
Bean ready to use
```

---

# 14. Key Observations

- Autowiring = automatic dependency resolution
- Works only with objects (not primitives/strings)
- Field injection happens after object creation
- ByType injection is default behavior
- Multiple beans → ambiguity error
- @Qualifier resolves ambiguity
- Spring always builds full object graph before giving bean

---

# Final Mental Picture

```text
YOU: I want Account
        ↓
Spring: OK
        ↓
I will create Account
        ↓
I see it needs Address
        ↓
I find Address bean
        ↓
I inject it
        ↓
Here is your fully ready object
```
