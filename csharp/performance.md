# Performance

Performance matters, but it should never come at the cost of readability or maintainability without clear evidence of need.  
Always **measure first** and use profiling tools before applying optimizations.

## General Rules

- ✅ **Measure before optimizing** – don’t assume where the bottleneck is. Use profilers (e.g., Visual Studio Profiler, dotTrace, BenchmarkDotNet).
- ✅ Focus optimizations only on **hot paths** (critical sections executed frequently).
- ✅ Keep the code simple until performance issues are proven.

## String Handling

Use `StringBuilder` when performing **heavy string concatenations** inside loops or when building large strings.

```csharp
// ✅ Efficient concatenation
var builder = new StringBuilder();
for (int i = 0; i < 1000; i++)
{
    builder.Append(i).Append(", ");
}
string result = builder.ToString();

// ❌ Inefficient concatenation
string result = "";
for (int i = 0; i < 1000; i++)
{
    result += i + ", ";
}
```

## Span<T> and Memory<T>

For high-performance scenarios (e.g., parsing, text processing, memory slicing), prefer `Span<T>` and `Memory<T>` to avoid unnecessary allocations.

```csharp
// ✅ Using Span<T>
ReadOnlySpan<char> slice = "HelloWorld".AsSpan(0, 5);
Console.WriteLine(slice.ToString()); // "Hello"
```

## Minimize Allocations

Allocations create **GC pressure**, which can hurt performance in tight loops or high-frequency code paths.  
To minimize allocations:

- Use **structs** for small, immutable value types.
- Use **ref struct** (e.g., `Span<T>`) for stack-only high-performance scenarios.

```csharp
// ✅ Struct for lightweight values
public readonly struct Point(int X, int Y);

// ✅ Ref struct for stack-only scenarios
public ref struct BufferSpan
{
    private Span<byte> _buffer;

    public BufferSpan(Span<byte> buffer) => _buffer = buffer;
}
```

## Summary

- ✅ Profile before optimizing — don’t guess.
- ✅ Use `StringBuilder` for heavy string concatenation.
- ✅ Use `Span<T>` / `Memory<T>` in performance-critical code.
- ✅ Prefer `struct` and `ref struct` for reducing allocations.
- ❌ Don’t over-optimize prematurely.
