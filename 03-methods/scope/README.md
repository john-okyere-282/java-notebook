# Scope

## What is Scope?
Scope is the region of code where a variable is accessible. Outside its scope, the variable does not exist — trying to access it is a compile error.

Java has three levels of scope:

| Level | Declared | Accessible |
|-------|----------|------------|
| Class-level (field) | Outside all methods | Everywhere in the class |
| Method-level | Inside a method | From declaration to end of method |
| Block-level | Inside `{}` block | From declaration to end of that block |

---

## Scope in Action

```java
public class ScopeDemo {
    static int classLevel = 100;  // class-level field — accessible everywhere in the class

    public static void main(String[] args) {
        int methodLevel = 50;     // only accessible inside main()

        for (int i = 0; i < 3; i++) {       // i is loop-scoped
            int blockLevel = i * 2;          // only accessible inside this loop body
            System.out.println(classLevel + " " + methodLevel + " " + blockLevel);
        }

        // i is NOT accessible here — compile error if you try
        // blockLevel is NOT accessible here — compile error if you try
        System.out.println(methodLevel);  // OK
        System.out.println(classLevel);   // OK
    }

    public static void anotherMethod() {
        // methodLevel is NOT accessible here
        System.out.println(classLevel);   // OK — class-level is shared
    }
}
```

---

## Variable Lifetime

| Level | Created | Destroyed |
|-------|---------|-----------|
| Class-level static | When the class loads | When the program ends |
| Method-level | When the method is called | When the method returns |
| Block-level | When the block is entered | When the block exits |

This is why local variables do not keep their values between method calls — they are created fresh each time.

---

## Shadowing
A local variable with the same name as a class-level field **shadows** (hides) the field within that scope.

```java
public class ShadowExample {
    static int value = 10;  // class-level

    public static void demonstrate() {
        int value = 99;  // local variable SHADOWS the class-level one
        System.out.println(value);        // prints 99 (local)
        System.out.println(ShadowExample.value);  // prints 10 (class-level, via class name)
    }
}
```

Shadowing is legal but confusing. Avoid it — use distinct names.

---

## Best Practice (Amalitech §7.4)

> 📖 **Official Amalitech Standard:** §7.4 — Declare variables as close as possible to where they are first used. Declare loop control variables inside the loop statement.

```java
// BAD: variable declared far from its first use
int result;
// ... 30 lines of unrelated code ...
result = calculate();

// GOOD: declare where you need it
int result = calculate();
```

```java
// BAD: loop variable declared outside
int i;
for (i = 0; i < 10; i++) { ... }

// GOOD: loop variable scoped to the loop
for (int i = 0; i < 10; i++) { ... }
// i does not pollute the surrounding scope
```

Declaring variables close to their use:
- Makes the code easier to read (variable and its usage are near each other)
- Limits the chance of accidentally reusing a stale value
- Makes refactoring easier (less code to move)

---

## Common Mistakes

```java
// MISTAKE 1: trying to use a variable outside its block
for (int i = 0; i < 5; i++) {
    // ...
}
System.out.println(i);  // compile error — i is out of scope

// MISTAKE 2: thinking a loop variable keeps its value next iteration
for (int i = 0; i < 3; i++) {
    int temp = i * 2;
    // temp is re-created fresh each iteration — it does not "remember" the previous value
}
```

---

## Exercises

**Exercise 1:** Write a program with a class-level variable `counter`. Write two methods: `increment()` (adds 1) and `printCounter()` (prints the value). Call them from `main()` and observe how the class-level variable persists between calls.

**Exercise 2:** Demonstrate scope with this scenario: write a method with a local variable `x = 5`. Inside a nested `if` block, declare another `x = 10`. Print both (use different names to avoid a compile error — Java does not allow re-declaring in a nested scope, only in a sibling scope). Then show that after the block, only the outer `x` is visible.

**Exercise 3:** Explain what this code prints and why:
```java
public static void mystery() {
    int result = 0;
    for (int i = 1; i <= 5; i++) {
        result += i;
    }
    System.out.println(result);
}
```
Then modify it so `result` is declared inside the loop. What happens, and why?
