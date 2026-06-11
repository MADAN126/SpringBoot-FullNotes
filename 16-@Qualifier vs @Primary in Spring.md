# @Qualifier vs @Primary in Spring (Deep Internal Understanding)

---

# 1. Situation (Why these annotations exist)

You have multiple beans of the same type:

```java
Address → multiple implementations / objects
```

Example:

```text
hyd Address
bangalore Address
```

Now Spring faces a problem:

```text
@Autowired Address
→ Which one should I inject?
```

This is called:

```text
ambiguity problem
```

---

# 2. Problem (What breaks without control)

```text
Spring finds multiple beans of same type
        ↓
Cannot decide which one to inject
        ↓
Throws error
```

```text
NoUniqueBeanDefinitionException
```

---

# 3. Solution Overview

Spring gives 2 tools:

| Annotation | Purpose |
|------------|--------|
| @Qualifier | Explicit selection (manual control) |
| @Primary   | Default bean selection |

---

# 4. @Qualifier (Exact Control)

## Situation

You want ONE specific bean.

---

## Rule

```text
Choose bean by NAME explicitly
```

---

## Beans

```java
@Bean("hyd")
Address hydAddress() { }

@Bean("banglore")
Address bangloreAddress() { }
```

---

## Injection

```java
@Autowired
@Qualifier("hyd")
private Address address;
```

---

# Internal Flow (@Qualifier)

```text
Step 1: byType search → Address
        ↓
Step 2: multiple beans found
        ↓
Step 3: apply @Qualifier("hyd")
        ↓
Step 4: filter bean = hyd
        ↓
Step 5: inject hyd Address
```

---

## Key Idea

```text
@Qualifier = I choose EXACT bean
```

✔ Highest priority control  
✔ Overrides everything else  

---

# 5. @Primary (Default Choice)

## Situation

You don’t specify anything.

---

## Rule

```text
Mark ONE bean as default
```

---

## Configuration

```java
@Bean("hyd")
Address hydAddress() { }

@Bean("banglore")
@Primary
Address bangloreAddress() { }
```

---

## Injection

```java
@Autowired
private Address address;
```

---

# Internal Flow (@Primary)

```text
Step 1: byType search → Address
        ↓
Step 2: multiple beans found
        ↓
Step 3: check @Primary
        ↓
Step 4: select banglore bean
        ↓
Step 5: inject it
```

---

## Key Idea

```text
@Primary = default fallback bean
```

---

# 6. Priority Rule (VERY IMPORTANT)

```text
@Qualifier > @Primary > byType
```

---

## Case Table

| Condition | Result |
|----------|--------|
| @Qualifier present | wins always |
| only @Primary | used as default |
| nothing | error if multiple beans |

---

# 7. Conflict Case (Both present)

```java
@Bean("hyd")
Address hydAddress()

@Bean("banglore")
@Primary
Address bangloreAddress()
```

Injection:

```java
@Autowired
@Qualifier("hyd")
private Address address;
```

---

## Result Flow

```text
@Qualifier applied
        ↓
Spring ignores @Primary
        ↓
hyd injected
```

---

## Rule

```text
@Qualifier overrides @Primary
```

---

# 8. Without @Qualifier (Only @Primary)

```java
@Autowired
private Address address;
```

```text
Spring sees multiple beans
        ↓
Checks @Primary
        ↓
Injects banglore
```

---

# 9. Internal Decision Flow (FULL MODEL)

```text
@Autowired detected
        ↓
Step 1: Find byType
        ↓
If ONE bean → inject
        ↓
If MULTIPLE beans:
        ↓
   Step 2: Check @Qualifier
        ↓
   If present → use it
        ↓
   Else check @Primary
        ↓
   If present → use it
        ↓
   Else ERROR
```

---

# 10. Mental Model

## @Qualifier

```text
"Use THIS exact bean"
```

## @Primary

```text
"Use THIS by default if nothing is specified"
```

---

# 11. Real-world Analogy

## @Qualifier

```text
You order:
"Give me Coke Zero"
→ exact selection
```

## @Primary

```text
Restaurant default drink = Pepsi
If you don’t say anything → Pepsi comes
```

---

# 12. Key Observations

- Both solve ambiguity
- @Qualifier = explicit choice
- @Primary = default choice
- Both apply AFTER byType search
- @Qualifier has highest priority

---

# Final One-line Understanding

👉 **@Qualifier tells Spring exactly which bean to inject, while @Primary defines a default bean when multiple candidates exist; if both are present, @Qualifier always wins.**
