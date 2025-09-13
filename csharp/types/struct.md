# Struct

Structs in C# are **value types** and should be used carefully.  
They are best suited for small, immutable values or performance-critical scenarios.

## General Rules

- ✅ **Always mark structs as `readonly`** – avoid mutable state. Structs should be immutable.
- ✅ Use structs for **single values** (e.g., `Point`, `Guid`) or **performance-critical code**.
- ❌ Avoid frequent **boxing** (when a struct is treated as an object or cast to a non-generic interface).
  - Use **generic constraints** on interfaces to limit boxing.
  - Use **`ref struct`** to avoid boxing completely (e.g., `Span<T>`).
- ✅ Keep the **state small** to reduce copying overhead when passing structs around.
- ✅ Implement a **custom `ToString()`** for better debugging and logging.
- ✅ Implement **`IEquatable<T>`** for performance and correctness:
  - `object.Equals` on value types causes boxing and uses reflection, which is inefficient. A custom `Equals` avoids boxing and improves performance.

## Example: Good Struct

```csharp
public readonly struct Point : IEquatable<Point>
{
    public int X { get; }
    public int Y { get; }

    public Point(int x, int y)
    {
        X = x;
        Y = y;
    }

    public override string ToString() => $"({X}, {Y})";

    public bool Equals(Point other) => X == other.X && Y == other.Y;

    public override bool Equals(object? obj) => obj is Point other && Equals(other);

    public override int GetHashCode() => HashCode.Combine(X, Y);

    public static bool operator ==(Point left, Point right) => left.Equals(right);
    public static bool operator !=(Point left, Point right) => !left.Equals(right);
}
```

## Example: Avoid Boxing with Generics

```csharp
public interface IMeasure<T>
    where T : struct, IMeasure<T>
{
    double Value { get; }
}

public readonly struct Distance(double meters) : IMeasure<Distance>
{
    public double Value { get; } = meters;
}
```

Here, the generic constraint (`where T : struct`) avoids boxing when working with value types.

## Example: `ref struct`

```csharp
public ref struct BufferSpan
{
    private Span<byte> _buffer;

    public BufferSpan(Span<byte> buffer) => _buffer = buffer;

    public void Fill(byte value) => _buffer.Fill(value);
}
```

`ref struct` types are always stack-allocated and **cannot be boxed**, ensuring zero allocations.

## Summary

- ✅ Prefer **`readonly struct`** for immutability.
- ✅ Keep structs **small and efficient**.
- ✅ Implement `ToString()` and `IEquatable<T>` to avoid boxing and improve performance.
- ✅ Use `ref struct` for stack-only, high-performance scenarios.
- ❌ Don’t use mutable structs or large structs that are frequently copied.
