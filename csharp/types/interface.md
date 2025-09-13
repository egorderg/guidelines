# Interface

Interfaces define **contracts** and should remain **focused, small, and meaningful**.  
They are most powerful when they express clear roles or traits, instead of being bloated "god interfaces."

## General Rules

- ✅ **Keep interfaces small** – prefer splitting large interfaces into multiple, focused ones.
- ✅ Use **adjectives** if the interface describes a **capability/trait** (e.g., `IDisposable`, `IReadable`).
- ✅ Use **nouns** if the interface represents a **role/contract** (e.g., `IRepository`, `IService`).
- ✅ Follow **.NET naming conventions**: prefix with `I`, keep names short and precise.
- ⚠️ Use **marker interfaces** only if required (e.g., to tag types for special processing).
- ⚠️ Add interfaces only when you truly need multiple impls or a seam for testing. Don’t **interface all the things**.

## Examples

### Small, Focused Interface

```csharp
public interface IReadable
{
    string Read();
}

public interface IWritable
{
    void Write(string data);
}
```

Better than combining both into a single `IReadWrite` if not all types need both behaviors.

### Role vs. Trait

```csharp
// Trait (adjective) – expresses capability
public interface ISerializable
{
    string Serialize();
}

// Role (noun) – expresses a contract
public interface IRepository<T>
{
    void Add(T item);
    T GetById(Guid id);
}
```

### Marker Interface

Marker interfaces carry **no members**, but serve as type tags.  
They should be used **sparingly**, and only when reflection or special framework integration requires it.

```csharp
public interface IMarker;
```

## Summary

- ✅ Split large interfaces into **smaller, focused contracts**.
- ✅ Use **adjectives** for traits and **nouns** for roles.
- ✅ Keep names short, precise, and follow **.NET conventions**.
- ⚠️ Marker interfaces are acceptable in rare cases, but use with caution.
