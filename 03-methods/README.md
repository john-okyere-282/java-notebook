# Phase 03 — Methods

A method is a named block of code that performs a specific task. Methods let you avoid repeating code, organise your program into logical steps, and test individual pieces of behaviour in isolation.

**Real-world analogy:** Think of methods like appliances. A toaster has one job: toast bread. A washing machine has one job: wash clothes. Each method should have one clearly defined job too (Amalitech §4.2).

## Why Methods Matter
Without methods, every program is one giant `main()`. With methods:
- You write code once and call it anywhere (**DRY** — Don't Repeat Yourself)
- You can test each method in isolation
- You can read the program top-down without being overwhelmed by detail
- Changing one piece of logic only requires changing one method

## Topics in this Phase

| Topic | Description |
|-------|-------------|
| [Creating methods](./creating-methods/README.md) | Anatomy, declaration, and calling |
| [Parameters and return types](./parameters-return-types/README.md) | Passing data in and out |
| [Method overloading](./method-overloading/README.md) | Same name, different signatures |
| [Scope](./scope/README.md) | Where variables live and die |
| [Static vs instance methods](./static-vs-instance/README.md) | Class-level vs object-level behaviour |
| [Recursion](./recursion/README.md) | Methods that call themselves |
| [Exercises](./exercises/README.md) | Practice problems for this phase |

## Prerequisites
Phase 02 — Control Flow (loops, if-else, switch)

## Learning Goals
After completing this phase you should be able to:
- Write and call methods with parameters and return values
- Choose the right return type for any method
- Understand and apply method overloading
- Explain variable scope and avoid scope-related bugs
- Distinguish static methods from instance methods and choose appropriately
- Write simple recursive methods with a correct base case
- Apply Amalitech method standards (§4): one job, short, good names, few parameters
