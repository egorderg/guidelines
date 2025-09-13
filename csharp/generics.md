# Generics

Generics in C# are one of the most powerful features of the language.  
They are not just about type safety — they allow the compiler to **generate efficient, type-specific code** at compile time.

## General Principles

- ✅ Generics act as a **code generator**: the compiler produces explicit implementations for each type combination, instead of relying on runtime abstractions.
- ✅ They **reduce boxing** when structs are passed into generic methods or classes with interface constraints, because the compiler can specialize the implementation.
- ✅ Using generics makes implementations more **extensible** without changing code — the implementation doesn’t care if the type is a class or a struct.
- ✅ Generics can create **type-safe containers** or utilities with minimal overhead.
- ⚠️ Avoid overcomplicating code with excessive generic constraints — prefer clarity over cleverness.

## Example: Generic Container with Static Implementation

This example shows how the compiler generates different implementations of the same generic structure depending on the type parameters.
It allows you to **store and manage actions** efficiently in a type-safe way.

```csharp
public static class ActionContainer<TWorld>
    where TWorld : IWorld
{
    public static class Collection<TAction>
        where TAction : IAction
    {
        private static readonly List<Action<TAction>> Handlers = [];

        public static void Add(Action<TAction> action)
        {
            Handlers.Add(action);
        }

        public static IEnumerable<Action<TAction>> GetAll()
        {
            return Handlers;
        }
    }
}

public sealed class ExampleWorld : IWorld;
public sealed class ExampleWorld2 : IWorld;
public sealed class ExampleAction : IAction;
public sealed class ExampleAction2 : IAction;

// ✅ Compiler generates specialized containers for each unique type combination
ActionContainer<ExampleWorld>.Collection<ExampleAction>.Add(action => { });
ActionContainer<ExampleWorld>.Collection<ExampleAction2>.Add(action => { });
ActionContainer<ExampleWorld2>.Collection<ExampleAction>.Add(action => { });
```

Here, the compiler generates **distinct static storage** for each combination of `<TWorld, TAction>`.  
This makes the code both **type-safe** and **high-performance**.

## Benefits of Generics

- **Performance**: Avoids boxing/unboxing for value types.
- **Safety**: Strong compile-time type checking, fewer runtime errors.
- **Flexibility**: One generic implementation works for many types.
- **Extensibility**: New types can be plugged in without touching existing code.

## Summary

- ✅ Use generics to **avoid duplication** and **improve performance**.
- ✅ Leverage generic constraints (`where T : ...`) to express contracts without runtime checks.
- ✅ Generics allow for **specialized static storage** (like containers or caches) per type combination.
- ❌ Don’t abuse generics for overly complex designs — keep it clear and intentional.
