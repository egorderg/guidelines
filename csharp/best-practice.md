# Best Practices

This section collects general recommendations for writing clean, safe, and modern C# code.  
They are not hard rules, but following them will help keep codebases consistent and maintainable.

## Operator Overloading

- ✅ Use operator overloading **only for mathematical constructs** (e.g., vectors, matrices, complex numbers).
- ❌ Don’t misuse it for domain logic — it makes code harder to read and maintain.

```csharp
// ✅ Correct usage
public readonly record struct Vector(double X, double Y)
{
    public static Vector operator +(Vector a, Vector b) => new(a.X + b.X, a.Y + b.Y);
}

// ❌ Misuse
public record Customer(string Id, string Name)
{
    public static Customer operator +(Customer a, Customer b) => Merge(a, b); // confusing
}
```

## Pattern Matching (Prefer over legacy switch)

- Prefer **pattern matching** and **switch expressions** over verbose, older-style `switch` statements.

```csharp
// ✅ Switch expression with patterns
string Describe(object? value) => value switch
{
    null        => "Null",
    int i       => $"Integer: {i}",
    string s    => $"String of length {s.Length}",
    _           => "Unknown"
};
```

## Nullable Reference Types (NRT)

- Always enable **nullable reference types** to make null contracts explicit and safer.

```csharp
string? name = null; // must be checked before use
```

_(Project-wide: set `<Nullable>enable</Nullable>` in your .csproj.)_

## Null Validation with Pattern Matching

- Prefer pattern matching (or built-in guard helpers) over `== null`.

```csharp
// ✅ Pattern matching
if (user is null)
{
    throw new ArgumentNullException(nameof(user));
}

// ✅ Modern guard (net6+)
ArgumentNullException.ThrowIfNull(user);

// ❌ Old style
if (user == null) { /* ... */ }
```

## Local Variables with `var`

- Use `var` when the type is **obvious** from the right-hand side.

```csharp
// ✅ Obvious type
var customer = new Customer("42", "Ada");

// ❌ Redundant
Customer customer = new Customer("42", "Ada");
```

## Asynchronous Code

- Use `async/await` rather than lower-level continuations for clarity.

```csharp
// ✅ Modern async
await DoWorkAsync();

// ❌ Noisy continuation
DoWorkAsync().ContinueWith(t => Handle(t));
```

## Constants and Immutability

- Prefer `readonly` fields and `const` values where applicable to express intent and prevent accidental changes.

```csharp
public sealed class Config
{
    public const int DefaultTimeoutSeconds = 30;
    private readonly string ConnectionString;

    public Config(string connectionString)
    {
        ConnectionString = connectionString;
    }
}
```

## LINQ Usage

- Use LINQ for **readability**.

```csharp
// ✅ Clear LINQ
var activeUsers = users.Where(u => u.IsActive).ToList();
```
