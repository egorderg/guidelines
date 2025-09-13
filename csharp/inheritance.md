# Inheritance

Inheritance is one of the most misused features in object-oriented design.  
In modern C#, prefer **composition with interfaces** over inheritance, unless you are forced to inherit from a base class by a third-party framework (e.g., ASP.NET controllers, WPF controls, EF entities).

## Why Avoid Inheritance?

- **Too generic, harder to test** – base classes often carry behavior that all subclasses inherit, even if they don’t need it.
- **Not all subclasses fit the same contract** – e.g., what if a `Control` doesn’t support children? The base class may still allow it, causing runtime exceptions.
- **Risk of “God classes”** – base classes tend to grow over time, accumulating responsibilities that should be split.
- **Rigid design** – inheritance chains are harder to change without breaking existing code.

## Bad Example (Inheritance)

```csharp
public abstract class Control
{
    private readonly List<Control> _children = new();

    public void Add(Control control)
    {
        _children.Add(control);
    }
}

public class Button : Control
{
}

var button = new Button();
button.Add(new Label());
```

**Problems:**

- A `Button` doesn’t necessarily support children, but it inherits that behavior anyway.
- If one subclass doesn’t support a base feature, it may throw runtime errors.
- Tests become harder, since behavior is shared implicitly.

## Preferred Approach: Composition + Interfaces

Instead of inheriting functionality, compose objects with smaller **interfaces** and **traits**.  
This makes code more flexible, testable, and explicit.

```csharp
public interface IControl;

public interface IComposite
{
    ControlChildren Children { get; }
}

public sealed class ControlChildren
{
    private readonly List<Control> _children = new();

    public void Add(Control control)
    {
        _children.Add(control);
    }
}

public sealed class Button : IControl, IComposite
{
    public ControlChildren Children { get; } = new();
}

var button = new Button();
button.Children.Add(new Label());
```

**Benefits:**

- More **extensible** – a type can implement multiple interfaces to gain behavior.
- Traits are explicit and can be **tested independently**.
- Interfaces can be checked via **pattern matching** to apply specific functionality only when available.

```csharp
if (control is IComposite composite)
{
    composite.Children.Add(new Button());
}
```

## Summary

- ✅ Prefer **composition with interfaces**
- ✅ Use **traits** to define optional behavior
- ✅ Apply **pattern matching** for feature detection
- ❌ Avoid inheritance unless required by external frameworks
