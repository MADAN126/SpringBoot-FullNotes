# Setter Injection vs Constructor Injection in Spring 
---

# 1. Situation (Why Different Injection Styles Exist)

A class often depends on other objects.

Example:

```text
MessageSender
      ↓
depends on
      ↓
MessageService
```

Spring must answer:

```text
WHEN should dependency be injected?
```

There are two common answers:

```text
1. After object creation  → Setter Injection
2. During object creation → Constructor Injection
```

---

# 2. Setter Injection

## Situation

Create object first.

Provide dependencies later.

---

## Problem it solves

Sometimes dependencies are:

```text
optional
or
changeable after creation
```

---

## Example

### Interface

```java
public interface MessageService {
    void sendMessage(String message);
}
```

---

### Implementations

```java
@Component("emailService")
public class EmailService implements MessageService {

    public void sendMessage(String message) {
        System.out.println(message);
    }
}
```

```java
@Component("smsService")
public class SMSService implements MessageService {

    public void sendMessage(String message) {
        System.out.println(message);
    }
}
```

---

### Setter Injection

```java
@Component
public class MessageSender {

    private MessageService messageService;

    @Autowired
    public void setMessageService(
            @Qualifier("emailService")
            MessageService messageService) {

        this.messageService = messageService;

        System.out.println("setter based dependency injection");
    }

    public void sendMessage(String message) {
        messageService.sendMessage(message);
    }
}
```

---

## Internal Flow

```text
Spring creates MessageSender
        ↓
MessageSender exists
        ↓
Find @Autowired setter
        ↓
Resolve emailService bean
        ↓
Call setMessageService(...)
        ↓
Dependency injected
```

---

## Flow Diagram

```text
Create Object
      ↓
Find Setter
      ↓
Resolve Dependency
      ↓
Call Setter
      ↓
Bean Ready
```

---

## Output

```text
setter based dependency injection
Hi, good morning have a nice day!.
```

---

## Key Observation

Spring does:

```java
MessageSender sender = new MessageSender();

sender.setMessageService(emailService);
```

Internally.

---

# 3. Multiple Dependencies using Setter Injection

## Situation

MessageSender needs two services.

---

## Code

```java
@Component
public class MessageSender {

    private MessageService messageService;
    private MessageService smsService;

    @Autowired
    public void setMessageService(
            @Qualifier("emailService")
            MessageService messageService) {

        this.messageService = messageService;

        System.out.println("setter based dependency injection");
    }

    @Autowired
    public void setSmsService(
            @Qualifier("smsService")
            MessageService smsService) {

        this.smsService = smsService;

        System.out.println("setter based dependency injection 2");
    }

    public void sendMessage(String message) {

        messageService.sendMessage(message);
        smsService.sendMessage(message);
    }
}
```

---

## Internal Flow

```text
Create MessageSender
        ↓
Call setMessageService()
        ↓
Inject EmailService
        ↓
Call setSmsService()
        ↓
Inject SMSService
        ↓
Bean Ready
```

---

## Output

```text
setter based dependency injection 2
setter based dependency injection

Hi, good morning have a nice day!.
Hi, good morning have a nice day!.
```

---

## Key Observation

```text
Multiple setters
        ↓
Multiple dependencies
        ↓
Spring injects one-by-one
```

---

# 4. Constructor Injection

## Situation

Dependency is REQUIRED.

Object should never exist without it.

---

## Problem with Setter Injection

Setter injection allows:

```java
new MessageSender();
```

even before dependencies arrive.

So object may exist incompletely.

---

# 5. Constructor Injection Solution

Inject dependency while creating object.

---

## Code

```java
@Component
public class MessageSender {

    private MessageService messageService;

    @Autowired
    public MessageSender(
            @Qualifier("emailService")
            MessageService messageService) {

        this.messageService = messageService;

        System.out.println(
                "constructor based dependency injection");
    }

    public void sendMessage(String message) {

        messageService.sendMessage(message);
    }
}
```

---

## Internal Flow

```text
Spring sees constructor
        ↓
Needs MessageService
        ↓
Find emailService
        ↓
Call constructor
        ↓
Create fully initialized object
```

---

## Flow Diagram

```text
Resolve Dependencies
        ↓
Call Constructor
        ↓
Create Object
        ↓
Bean Ready
```

---

## Output

```text
constructor based dependency injection

Hi, good morning have a nice day!.
```

---

## Key Observation

Spring internally does:

```java
new MessageSender(emailService);
```

instead of:

```java
new MessageSender();

sender.setMessageService(...);
```

---

# 6. Setter vs Constructor

| Feature | Setter Injection | Constructor Injection |
|----------|------------------|------------------------|
| Injection Time | After creation | During creation |
| Object State | Can be incomplete | Always complete |
| Dependency Nature | Optional | Required |
| Internal Method Used | Setter method | Constructor |
| Supports Multiple Dependencies | Yes | Yes |
| Recommended Usage | Optional dependencies | Mandatory dependencies |

---

# 7. How Annotation-Based Autowiring Maps to XML Modes

Spring XML had:

```text
no
byName
byType
constructor
```

Modern Spring achieves the same behavior differently.

---

## no

### XML

```xml
autowire="no"
```

### Annotation Equivalent

```java
private Address address;
```

No:

```java
@Autowired
```

---

## Internal Flow

```text
Spring creates bean
        ↓
No @Autowired found
        ↓
No injection
```

---

## byType

### XML

```xml
autowire="byType"
```

### Annotation Equivalent

```java
@Autowired
private Address address;
```

---

## Internal Flow

```text
Find dependency type
        ↓
Search matching bean type
        ↓
Inject
```

---

## byName

### XML

```xml
autowire="byName"
```

### Annotation Equivalent

```java
@Autowired
@Qualifier("emailService")
private MessageService service;
```

---

## Internal Flow

```text
Search by type
        ↓
Multiple beans found
        ↓
Qualifier chooses exact bean
        ↓
Inject
```

---

## Important Clarification

```text
XML byName
=
automatic bean-name matching

Annotation + Qualifier
=
explicit bean-name selection
```

They achieve similar outcomes but work differently.

---

## constructor

### XML

```xml
autowire="constructor"
```

### Annotation Equivalent

```java
@Autowired
public Employee(Address address) {
    this.address = address;
}
```

---

## Internal Flow

```text
Resolve constructor parameters
        ↓
Call constructor
        ↓
Create bean
```

---

# 8. Full Spring Injection Lifecycle

```text
@ComponentScan
        ↓
Detect Components
        ↓
Create Bean Definitions
        ↓
Instantiate Beans
        ↓
Find @Autowired
        ↓
Resolve Dependencies
        ↓
Apply @Qualifier/@Primary if needed
        ↓
Inject Dependencies
        ↓
Store Fully Ready Beans
        ↓
Application Uses Beans
```

---

# 9. Final Mental Model

```text
Setter Injection
        ↓
Create object first
        ↓
Fill dependencies later

Constructor Injection
        ↓
Collect dependencies first
        ↓
Create object fully ready
```

---

# 10. One-Line Understanding

👉 Spring annotation-based autowiring replaces XML autowiring modes by using `@Autowired`, `@Qualifier`, and constructors to automatically resolve and inject dependencies either after object creation (setter) or during object creation (constructor).
