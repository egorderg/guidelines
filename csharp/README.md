# C# Coding Guidelines

This document defines conventions, patterns, and best practices for writing clean, consistent, and maintainable C# code.  
The goal is to help developers write code that is **readable, reliable, testable, and easy to maintain**.

## Table of Contents

1. [General Principles](#general-principles)
2. [Naming Conventions](#naming-conventions)
3. [Formatting and Style](#formatting-and-style)
4. [Comments and Documentation](#comments-and-documentation)
5. [Simplicity Principles](#simplicity-principles)
6. [Resources](#resources)

## General Principles

- Prefer **readability over cleverness**. Code should be easily understood by others.
- Follow the principle of **least surprise** – code should behave as expected.
- Keep methods **short and focused** on a single responsibility.
- Use meaningful names that communicate intent.
- Avoid premature optimization; focus on **clarity first**.

## Naming Conventions

- **Classes & Interfaces**: Use `PascalCase` (e.g., `CustomerRepository`, `ILogger`).
- **Methods & Properties**: Use `PascalCase` (e.g., `GetCustomer()`, `IsEnabled`).
- **Fields & Variables**: Use `camelCase` (e.g., `customerId`, `retryCount`).
- **Constants**: Use `PascalCase` (e.g., `DefaultTimeout`, `MaxRetry`).
- **Async Methods**: Suffix with `Async` (e.g., `SaveChangesAsync`).
- Avoid abbreviations unless widely known (e.g., `Id`, `Xml`, `Http`).

## Formatting and Style

- Indentation: **2 spaces**, no tabs.
- Line length: aim for **<90 characters** per line.
- Braces: always use braces, even for single-line `if/else`.
- Editorconfig: always generate a default config with `dotnet new create editorconfig`

```toml
[*.cs]
indent_size = 2
tab_width = 2
max_line_length = 90
```

## Comments and Documentation

- Use **XML documentation comments** for public APIs.
- Write comments to explain **why**, not **what**.
- Avoid redundant comments. The code itself should be self-explanatory.

## Simplicity Principles

[**Test-Driven Development (TDD)**](testing/unit-testing.md) and [**unit testing**](testing/unit-testing.md) naturally guide developers toward **KISS** and **YAGNI**.

- Writing a failing test first forces you to implement **only the code required** to pass the test (supports **YAGNI**).
- Simple, testable units encourage smaller, focused methods and classes (supports **KISS**).
- Tests act as a safety net, allowing you to **refactor with confidence**, keeping designs simple instead of defensive.
- Overly complex or speculative code is harder to test — if it’s painful to test, it may be a sign you’re breaking **KISS** or **YAGNI**.

### KISS – _Keep It Simple, Stupid_

- Strive for the **simplest solution** that works.
- Avoid unnecessary abstractions, overengineering, or clever tricks.
- Readability and maintainability are more important than showing off technical skills.

### YAGNI – _You Aren't Gonna Need It_

- Don't build functionality until it is **actually required**.
- Resist the temptation to add "just in case" features.
- Every line of code has a cost: more to maintain, more to test, more to refactor.

## Resources

### General

- [Best Practice](best-practice.md)
- [Inheritance](inheritance.md)
- [Generics](generics.md)
- [Extensions](extensions.md)
- [Performance](performance.md)
- [Error Handling](error-handling.md)

### Types

- [Class](types/class.md)
- [Struct](types/struct.md)
- [Record](types/record.md)
- [Interface](types/interface.md)

### Testing

- [Unit Tests](testing/unit-testing.md)
- [Integration Tests](testing/integration-testing.md)

### Architectures

- [Gateway & Adapter](architectures/gateway-adapter.md)
- [Vertical Slice](architectures/vertical-slice.md)

### External

- [Microsoft C# Coding Conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions)
- [Framework Design Guideliens](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/)
