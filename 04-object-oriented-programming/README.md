# Phase 04 — Object-Oriented Programming

OOP is a way of organising your code around real-world things (objects). Instead of writing one long list of instructions, you model your program as a collection of objects — each with its own data (fields) and behaviour (methods).

## Why OOP?

Before OOP, programs were written as long sequences of steps (procedural programming). As programs grew into tens of thousands of lines, they became impossible to understand, change, or reuse. A bug in one place could break something completely unrelated.

OOP organises code into self-contained units:
- Each unit (class) is responsible for its own data
- Units communicate through well-defined interfaces
- Changes to one unit don't ripple unexpectedly through everything else

## The Four Pillars of OOP

| Pillar | Simple meaning |
|--------|---------------|
| **Encapsulation** | Bundle data and methods together; hide the internals and control access |
| **Inheritance** | A class can inherit fields and methods from another class |
| **Polymorphism** | The same method name does different things depending on the object |
| **Abstraction** | Show only what is necessary; hide complexity behind a clean interface |

## Topics in this Phase

| Topic | Description |
|-------|-------------|
| [Classes and objects](./classes-and-objects/README.md) | Blueprints and instances |
| [Constructors](./constructors/README.md) | Setting up objects correctly |
| [Encapsulation](./encapsulation/README.md) | Hiding state; controlling access |
| [Inheritance](./inheritance/README.md) | Sharing behaviour through parent/child classes |
| [Polymorphism](./polymorphism/README.md) | One name, many forms |
| [Abstraction](./abstraction/README.md) | Abstract classes; hiding complexity |
| [Interfaces](./interfaces/README.md) | Contracts that classes agree to fulfil |
| [Composition](./composition/README.md) | Building with has-a instead of is-a |
| [Exercises](./exercises/README.md) | Practice problems for this phase |

## Prerequisites
Phase 03 — Methods (parameters, return types, static vs instance)

## Learning Goals
After completing this phase you should be able to:
- Model real-world problems as classes with fields and methods
- Write constructors and use `this` correctly
- Apply access modifiers (`private`, `protected`, `public`) deliberately
- Use inheritance with `extends`, `super`, and `@Override`
- Explain and demonstrate all four OOP pillars with examples
- Choose between abstract class and interface based on the design need
- Prefer composition over inheritance where appropriate (Amalitech §2.2)
- Apply Amalitech standards: §8 (keep fields private), §2.2 (composition over inheritance)
