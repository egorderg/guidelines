# Class

Classes are the **workhorses of behavior**.

- **Interfaces** define contracts.
- **Structs** represent single values or performance-critical data.
- **Records** are data transfer objects (DTOs).
- **Classes** implement **behavior** that operates on or coordinates these other types.

## General Rules

- ✅ **Always seal classes** unless a framework (e.g., EF Core, ASP.NET) requires inheritance.
- ✅ Keep classes **small and cohesive** – follow the _Single Responsibility Principle (SRP)_.
- ✅ Keep state **minimal** – prefer working with DTOs or extracting logic into separate services/components.
- ✅ Use the **new primary constructor feature** (C# 12+) to simplify class initialization.
- ✅ Expose injected dependencies as properties when needed.
- ⚠️ Use **static classes sparingly**:
  - Appropriate for utility methods (e.g., `System.IO.File`).
  - Commonly used to hold extension methods.
  - Useful when no state or instance behavior is required.
  - Overuse reduces testability and flexibility.

## Example: Small, Sealed Service Class

```csharp
public sealed class ExampleService(IExternal external)
{
    public IExternal External => external;

    public void Run()
    {
        External.Execute();
    }
}
```

- Uses the **primary constructor** to inject a dependency.
- Exposes it via a read-only property.
- Keeps logic focused and testable.

## Example: When to Use Static Classes

```csharp
public static class Math
{
    public static double DegreesToRadians(double degrees) => degrees * Math.PI / 180.0;
}
```

- ✅ Good use: **stateless helper** functions.
- ❌ Bad use: **business logic** or stateful functionality – should live in proper services.

## Summary

- ✅ Use **sealed classes** for behavior unless external frameworks require inheritance.
- ✅ Keep classes **small, cohesive, and testable**.
- ✅ Prefer DTOs or separate services for data-heavy state.
- ✅ Use **primary constructors** to reduce boilerplate.
- ⚠️ Use **static classes** only for utilities, extension methods, or framework-style helpers.
