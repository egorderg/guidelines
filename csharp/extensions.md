# Extensions

Extension methods are a powerful way to add **utility-like behavior** to existing types without modifying their source.  
They should remain **pure, lightweight, and discoverable** — not a dumping ground for unrelated helpers.

## When to Use Extensions

- ✅ Adding **pure, reusable behavior** to a type you don’t own (BCL or third-party).
- ✅ The behavior is **utility-like** and reads fluently at call sites (LINQ-style).
- ✅ The method **logically belongs to the extended type** (mental model: “on the thing”).
- ✅ You need a tiny **adapter to bridge two APIs** without introducing dependencies.
- ✅ The operation is **stateless and side-effect free** (or has obvious, expected effects).
- ✅ Extending **interfaces with pure methods** for cross-cutting helpers.

**Example:**

```csharp
public static class StringExtensions
{
    public static bool IsNullOrEmptyOrWhiteSpace(this string? value)
        => string.IsNullOrWhiteSpace(value);
}

// Usage
if (input.IsNullOrEmptyOrWhiteSpace()) { ... }
```

## When Not to Use Extensions

- ❌ You **own the type** → add an instance method instead (better encapsulation & discoverability).
- ❌ Performs **I/O, logging, or networking implicitly** → surprising side effects.
- ❌ Changes **global state** or mutates input in non-obvious ways.
- ❌ **Domain-specific logic** → should live in the domain model (entity/value object).
- ❌ Collides with **common BCL/LINQ names** (`ToList`, `Select`, `Where`) without adding value.
- ❌ Requires **configuration or lifetime management** → should be a service, not an extension.

**Bad Example:**

```csharp
// ❌ Hidden I/O inside an extension
public static class ObjectExtensions
{
    public static void SaveToDisk(this object obj, string path)
    {
        File.WriteAllText(path, JsonSerializer.Serialize(obj));
    }
}
```

## Summary

- ✅ Keep extensions **pure, reusable, and stateless**.
- ✅ Use them to extend **types you don’t own** or to add **fluent helpers**.
- ❌ Don’t use them for **domain logic, I/O, or service-like behavior**.
- ❌ Avoid **namespace collisions** with existing LINQ/BCL methods.
