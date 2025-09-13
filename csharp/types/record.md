# Record

A **record** is a `class` or `struct` with compiler-generated features:

- Value-based equality (`==`, `Equals`, `GetHashCode`)
- `ToString()` with property printing
- A deconstructor
- `with` expressions for non-destructive mutation

They are best used for **immutable domain models, DTOs, or discriminated unions**.

## General Rules

- ✅ Always mark records as **sealed** unless inheritance is required.
- ✅ Use **required properties** if the constructor gets too big.
- ✅ Avoid **mutable data** – treat records as immutable.
- ✅ Use records if you care about **what the object represents**, not its identity (e.g., `Money`, `PersonName`, `Address`).
- ✅ Prefer records for:

  - Domain logic
  - DTOs
  - Discriminated unions

- ⚠️ For **core types** where you need finer control:
  - Use **struct** (e.g., `Vector2`, `Point`) for performance-critical small data.
  - Use **class** if the type defines **behavior**.

## Choosing Between Class, Struct, and Record

- Do you want **value-based equality** and immutability by default? → **Use a record**.
- Do you want **reference identity** and potentially mutable state? → **Use a class**.
- Does it represent a **core primitive type** (small, data-only)? → **Use a struct**.
- Does it contain **behavior**? → **Use a class**.

## Record Flavors

- **Record class** – reference type (default).
- **Record struct** – value type.

```csharp
public sealed record Person(string FirstName, string LastName);

public record struct Point(int X, int Y);
```

## Example: Required Properties

```csharp
public sealed record Person
{
    public required string FirstName { get; init; }
    public required string LastName { get; init; }
}
```

## Example: Discriminated Union

Records work especially well for **discriminated unions**, modeling outcomes explicitly without exceptions.

```csharp
public interface ISerializerResult<T>
{
    public sealed record Success(T Configuration) : ISerializerResult<T>;
    public sealed record Error(string Message) : ISerializerResult<T>;
}
```

Usage:

```csharp
ISerializerResult<MyConfiguration> result = Deserialize(configJson);
MyConfiguration? config = result switch
{
		ISerializerResult<MyConfiguration>.Success(var cfg) => cfg,
		ISerializerResult<MyConfiguration>.Error(var message) => HandleError(message)
};

static MyConfiguration? HandleError(string message)
{
	logger.Log(message);
	return default;
}
```

---

## Summary

- ✅ Use **sealed records** for immutable, value-based data.
- ✅ Use records for **DTOs, domain models, and discriminated unions**.
- ✅ Use **required properties** to avoid bloated constructors.
- ⚠️ Use **class/struct** when behavior, performance, or finer control is needed.
- ❌ Don’t add mutable state to records — keep them immutable.
