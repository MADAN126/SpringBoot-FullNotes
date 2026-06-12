# Autowiring with Interface & Implementations 

---

# 1. Situation (Why Interface Autowiring is Needed)

You design your system using interfaces:

```java
Animal → contract
Tiger, Lion → implementations
```

```text
Animal = WHAT to do
Tiger/Lion = HOW it is done
```

Now you want:

```java
Animal animal;
```

But actual object should come dynamically.

---

# 2. Problem (What Spring must solve)

Now Spring sees:

```text
Animal → interface (no object)
Tiger → implementation
Lion  → implementation
```

So problem becomes:

```text
Which implementation should be injected?
```

---

# 3. Solution (Spring Behavior)

Spring follows this rule:

```text
Inject by TYPE
```

So:

```text
Animal → find all beans implementing Animal
```

---

# 4. Case 1: Only ONE Implementation

## Code

```java
@Component
class Tiger implements Animal { }
```

```java
@Autowired
Animal animal;
```

---

## Internal Flow

```text
Step 1: Detect Animal type dependency
        ↓
Step 2: Find beans of type Animal
        ↓
Step 3: Only Tiger found
        ↓
Step 4: Inject Tiger
```

---

## Result

```text
I am a Tiger
```

✔ No ambiguity → automatic injection works

---

# 5. Case 2: Multiple Implementations (PROBLEM)

## Code

```java
@Component("tiger")
class Tiger implements Animal {}

@Component("lion")
class Lion implements Animal {}
```

```java
@Autowired
Animal animal;
```

---

## Internal Flow

```text
Step 1: Find Animal type
        ↓
Step 2: Found 2 beans:
        - tiger
        - lion
        ↓
Step 3: Confusion
        ↓
Step 4: ERROR
```

---

## Error

```text
NoUniqueBeanDefinitionException
expected single matching bean but found 2
```

---

# 6. Why error happens

```text
Interface → multiple implementations
Spring cannot guess correct one
```

---

# 7. Solution 1: @Qualifier (Explicit Choice)

## Code

```java
@Autowired
@Qualifier("lion")
Animal animal;
```

---

## Flow

```text
Animal type search
        ↓
Multiple beans found
        ↓
Qualifier = lion
        ↓
Inject Lion only
```

---

## Result

```text
I am a Lion
```

✔ Manual control over selection

---

# 8. Solution 2: @Primary (Default Choice)

## Code

```java
@Component
@Primary
class Tiger implements Animal {}
```

```java
@Component
class Lion implements Animal {}
```

```java
@Autowired
Animal animal;
```

---

## Internal Flow

```text
Step 1: Find Animal beans
        ↓
Step 2: Multiple found
        ↓
Step 3: Check @Primary
        ↓
Step 4: Pick Tiger
```

---

## Result

```text
I am a Tiger
```

✔ Default implementation selected

---

# 9. Priority Rule (VERY IMPORTANT)

```text
@Qualifier > @Primary > byType
```

---

# 10. If BOTH exist

```java
@Primary on Tiger
@Qualifier("lion") used
```

## Result:

```text
Lion wins
```

✔ Qualifier always overrides Primary

---

# 11. Full Internal Flow (Interface Injection)

```text
@Autowired Animal
        ↓
Step 1: Find all Animal implementations
        ↓
Step 2: Check results
        ↓
IF only 1 → inject directly
        ↓
IF multiple:
        ↓
    Check @Qualifier
        ↓
    If present → use it
        ↓
    Else check @Primary
        ↓
    Else ERROR
```

---

# 12. Mental Model (VERY IMPORTANT)

## Interface Injection

```text
Animal reference
      ↓
Spring asks:
"Which implementation should I use?"
      ↓
Answer depends on rules:
```

| Situation | Result |
|----------|--------|
| One implementation | Auto inject |
| Multiple + @Qualifier | Specific bean |
| Multiple + @Primary | default bean |
| Multiple + none | ERROR |

---

# 13. Key Observations

- Interface injection is always byType internally
- Spring looks for implementations
- Multiple implementations = ambiguity problem
- @Qualifier gives direct control
- @Primary gives default choice
- Interface = abstraction, Spring = resolver

---

# Final One-line Understanding

👉 **When autowiring an interface, Spring injects one of its implementations based on byType; if multiple implementations exist, it uses @Qualifier for explicit selection or @Primary for default selection, otherwise it throws an ambiguity error.**
