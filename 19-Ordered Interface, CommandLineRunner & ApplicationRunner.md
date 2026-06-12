# Ordered Interface, CommandLineRunner & ApplicationRunner

---

# 1. Situation (Why Ordering Exists)

Spring Boot automatically discovers many beans.

Example:

```text
Runner 1 → Load Initial Data
Runner 2 → Configure Cache
Runner 3 → Start Background Jobs
```

But Spring now faces a question:

```text
Who should execute first?
```

Sometimes execution order matters.

---

# 2. Problem

Without ordering:

```text
Background jobs may start
before data initialization.
```

Example:

```text
Start Email Scheduler
        ↓
Database data not loaded yet
        ↓
Failure
```

Spring needs a way to decide:

```text
Execution Priority
```

---

# 3. Solution: Ordered Interface

Spring provides:

```java
org.springframework.core.Ordered
```

It allows beans to define their own priority.

---

# 4. Ordered Interface

## Situation

You want ordering through Java code.

---

## Code

```java
@Component
public class MyOrderedComponent implements Ordered {

    @Override
    public int getOrder() {

        return 1;
    }
}
```

---

# Internal Flow

```text
Spring discovers bean
        ↓
Checks if bean implements Ordered
        ↓
Calls getOrder()
        ↓
Gets value = 1
        ↓
Sorts beans
```

---

# Rule

```text
Lower value
=
Higher priority
```

---

## Example

```java
Runner A → getOrder() = 2
Runner B → getOrder() = 1
Runner C → getOrder() = -1
```

---

## Execution Order

```text
Runner C (-1)
        ↓
Runner B (1)
        ↓
Runner A (2)
```

---

# Key Observation

Ordered does NOT mean:

```text
Bean created first
```

It means:

```text
Bean processed first
in order-sensitive scenarios.
```

---

# Where Ordered Is Used

```text
CommandLineRunner
ApplicationRunner
Filters
Event Listeners
Interceptors
Other Spring processing chains
```

---

# 5. Runners in Spring Boot

## Situation

You want some code to execute automatically after startup.

Example:

```text
Insert sample data
Load cache
Validate configuration
Start background tasks
```

---

# Problem

main() only starts Spring.

```java
SpringApplication.run(...)
```

But:

```text
Where should startup logic go?
```

---

# Solution

Spring Boot provides:

```text
Runners
```

These execute automatically after startup.

---

# 6. Types of Runners

| Runner | Receives | Best For |
|----------|-----------|-----------|
| CommandLineRunner | String... args | Simple startup tasks |
| ApplicationRunner | ApplicationArguments | Advanced argument handling |

---

# Startup Lifecycle

```text
main()
    ↓
SpringApplication.run()
    ↓
ApplicationContext Created
    ↓
Beans Initialized
    ↓
Runners Executed
    ↓
Application Ready
```

---

# 7. CommandLineRunner

## Situation

Execute code once after Spring Boot starts.

---

## Code

```java
@Component
public class MyCommandLineRunner
        implements CommandLineRunner {

    @Override
    public void run(String... args) {

        System.out.println(
                "Hi from CommandLineRunner!");
    }
}
```

---

# Internal Flow

```text
Spring Boot Starts
        ↓
Create Beans
        ↓
Find CommandLineRunner beans
        ↓
Call run(args)
```

---

# Flow Diagram

```text
SpringApplication.run()
        ↓
ApplicationContext Ready
        ↓
CommandLineRunner Found
        ↓
run() Executes
```

---

# Output

```text
Hi from CommandLineRunner!
```

---

# What Spring Internally Does

Similar to:

```java
for(CommandLineRunner runner : runners){

    runner.run(args);
}
```

---

# Why Use CommandLineRunner?

```text
Database seeding
Loading test data
Printing startup information
Starting scheduled jobs
Cache warm-up
```

---

# 8. Multiple CommandLineRunners

## Situation

You have multiple startup tasks.

Example:

```text
Runner 1 → Load Data
Runner 2 → Initialize Cache
Runner 3 → Start Jobs
```

---

# Problem

Which runner executes first?

---

# Solution

Use:

```text
@Order
or
Ordered
```

---

# Using @Order

```java
@Component
@Order(2)
public class RunnerOne
        implements CommandLineRunner {

    @Override
    public void run(String... args) {

        System.out.println("Runner One");
    }
}
```

---

```java
@Component
@Order(1)
public class RunnerTwo
        implements CommandLineRunner {

    @Override
    public void run(String... args) {

        System.out.println("Runner Two");
    }
}
```

---

# Internal Flow

```text
Find all runners
        ↓
Read order values
        ↓
Sort ascending
        ↓
Execute
```

---

# Output

```text
Runner Two
Runner One
```

---

# Using Ordered Interface

```java
@Component
public class MyCommandLineRunnerTwo
        implements CommandLineRunner, Ordered {

    @Override
    public void run(String... args) {

        System.out.println(
                "Hi from MyCommandLineRunnerTwo!");
    }

    @Override
    public int getOrder() {

        return 1;
    }
}
```

---

# Internal Flow

```text
Runner discovered
        ↓
Spring calls getOrder()
        ↓
Receives 1
        ↓
Sorts runners
        ↓
Executes run()
```

---

# 9. ApplicationRunner

## Situation

You need command-line arguments in a structured form.

---

# Problem with CommandLineRunner

It gives:

```java
String... args
```

Example:

```text
--env=prod
--debug
file1.txt
```

becomes:

```java
args[0]
args[1]
args[2]
```

You must parse manually.

---

# Solution

Use:

```java
ApplicationRunner
```

It provides:

```java
ApplicationArguments
```

which understands arguments.

---

# Code

```java
@Component
public class MyRunners
        implements ApplicationRunner {

    @Override
    public void run(
            ApplicationArguments args)
            throws Exception {

        System.out.println(
                "Using ApplicationRunner");

        System.out.println(
                "Non-option args: "
                + args.getNonOptionArgs());

        System.out.println(
                "Option names: "
                + args.getOptionNames());

        System.out.println(
                "Option values: "
                + args.getOptionValues("myOption"));
    }
}
```

---

# Internal Flow

```text
Spring Boot Starts
        ↓
Parses command-line arguments
        ↓
Creates ApplicationArguments
        ↓
Finds ApplicationRunner
        ↓
Calls run(arguments)
```

---

# Example

Application Started As:

```text
java -jar app.jar
--env=prod
--debug
input.txt
```

---

# ApplicationArguments Provides

## Option Names

```java
args.getOptionNames()
```

Output:

```text
env
debug
```

---

## Option Values

```java
args.getOptionValues("env")
```

Output:

```text
prod
```

---

## Non-option Arguments

```java
args.getNonOptionArgs()
```

Output:

```text
input.txt
```

---

# Why ApplicationRunner?

Because Spring does the parsing for you.

Instead of:

```java
args[0]
args[1]
args[2]
```

you get:

```text
Option names
Option values
Non-option arguments
```

directly.

---

# 10. CommandLineRunner vs ApplicationRunner

| Feature | CommandLineRunner | ApplicationRunner |
|----------|-------------------|--------------------|
| Method Parameter | String... args | ApplicationArguments |
| Argument Handling | Raw strings | Structured |
| Parsing Support | Manual | Built-in |
| Simplicity | Easier | More powerful |
| Best Use Case | Simple startup logic | Complex CLI applications |

---

# 11. Complete Startup Flow

```text
main()
    ↓
SpringApplication.run()
    ↓
Create ApplicationContext
    ↓
Component Scan
    ↓
Bean Creation
    ↓
@Autowired Injection
    ↓
ApplicationArguments Prepared
    ↓
Find Runners
    ↓
Apply @Order / Ordered
    ↓
Execute run()
    ↓
Application Fully Ready
```

---

# 12. Key Observations

- Ordered controls processing priority.
- Lower values execute first.
- Ordered and @Order provide similar functionality.
- Runners execute automatically after startup.
- CommandLineRunner receives raw arguments.
- ApplicationRunner receives structured arguments.
- Multiple runners can be ordered.
- Runners are commonly used for initialization tasks.

---

# Final Mental Picture

```text
Spring Boot Starts
        ↓
Creates Beans
        ↓
Injects Dependencies
        ↓
Parses Arguments
        ↓
Finds Startup Runners
        ↓
Sorts Them
        ↓
Executes Their run() Methods
        ↓
Application Begins Serving Requests
```

---

# One-Line Understanding

👉 **Ordered/@Order decide WHO runs first, while CommandLineRunner and ApplicationRunner decide WHAT code should automatically run after Spring Boot starts.**
