# Project Structure & Architecture

Design for **clarity, testability, and replaceability**. Favor a clean separation between **domain (core)** and **infrastructure** via small, explicit contracts.

## Core Principles

- **Start with the domain**  
  Create domain logic first using **abstractions/adapters** and **DTOs**. Keep the domain free of technical details.

- **Core is contracts + DTOs only**  
  Place all **domain objects**, **DTOs**, and **contracts/interfaces** in `Core`.  
  ❌ No behavior tied to infrastructure. This enables custom implementations in separate projects.

- **Group by feature**  
  Organize folders (and optionally projects) by **feature** (e.g., `Rendering`, `UI`, `Storage`, `Math`) rather than by technical layer.

- **Adapters pattern**  
  Define adapter **contracts** (tiny interfaces that wrap .NET/BCL/static services like time, file, env) in `Core/Adapters`.  
  Implement them in `Adapters/` (root or per-feature) so domain code depends only on **interfaces**, not implementations.

- **Keep classes purposeful**  
  Don’t suffix everything with `Service`. Use meaningful names aligned with the role.  
  Put extension methods in a dedicated `Extensions/` folder or project.

- **Dependency direction**  
  Features and infrastructure depend on **Core**, never the other way around.  
  The **composition root** (Bootstrap/App) wires everything together (DI, configuration).

## Adapters: What & Why

- An **Adapter** is a tiny interface that wraps framework/static APIs (e.g., `IClock`, `IFileSystem`, `IEnvironment`).
- They allow **testing without hitting the OS** and keep domain code **technology-agnostic**.
- If an adapter simply forwards to well-tested BCL APIs, tests for the adapter can be **minimal or skipped**, while still enabling mocking/stubbing at higher levels.

Example (contract in Core):

```csharp
public interface IClock
{
    DateTimeOffset Now { get; }
}
```

Example (implementation in Adapters):

```csharp
public sealed class SystemClock : IClock
{
    public DateTimeOffset Now => DateTimeOffset.UtcNow;
}
```

## Single Project (Grouped by Features)

```text
src/
  YourApp/
    Core/                        # Domain primitives & cross-feature contracts (no tech deps)
      Adapters/                  # Tiny interfaces wrapping .NET/BCL/static (time, file, env)
      Extensions/                # Global extensions used across the app
      Rendering/
      UI/
    Adapters/                    # Adapter implementations, shared between features
    Rendering/                   # Feature implementing contracts from Core
      Extensions/
      Pipelines/
      Materials/
      Ports/
      Adapters/                  # Feature-scoped adapters (if needed)
    UI/                          # Feature implementing contracts from Core
      Widgets/
      Screens/
    Math/                        # Feature implementing contracts from Core
      Geometry/
      Numerics/
    Storage/                     # Feature implementing contracts from Core
    Bootstrap/                   # Composition root (DI, config)
    YourApp.csproj
tests/
  YourApp.Tests/
    Core/
    Rendering/
    UI/
  YourApp.Tests.csproj
.editorconfig
.gitignore
your-app.sln
```

**Notes**

- Keep **Core** free of infrastructure references.
- Place **feature-specific** files inside their feature folders.
- The **Bootstrap** layer wires the app with DI, config, logging, etc.

## Multi-Project (Grouped by Features)

```text
src/
  YourApp.Core/                  # Domain primitives & cross-feature contracts (no tech deps)
    Adapters/                    # Interfaces abstracting internal functionality (file, http, env)
    Rendering/
    UI/
    YourApp.Core.csproj
  YourApp.Adapters/              # Shared adapter implementations (wrap .NET/BCL/static)
    YourApp.Adapters.csproj
  YourApp.Rendering/             # Feature implementing Core contracts
    Pipelines/
    Materials/
    Ports/
    Adapters/                    # Feature-scoped adapters (if needed)
    YourApp.Rendering.csproj
  YourApp.UI/                    # Feature implementing Core contracts
    Widgets/
    Screens/
    YourApp.UI.csproj
  YourApp.Math/                  # Feature implementing Core contracts
    Geometry/
    Numerics/
    YourApp.Math.csproj
  YourApp.Storage/               # Feature implementing Core contracts
    YourApp.Storage.csproj
  YourApp.App/                   # Composition root (DI, config)
    YourApp.App.csproj
tests/
  YourApp.Core.Tests/
    YourApp.Core.Tests.csproj
  YourApp.Rendering.Tests/
    YourApp.Rendering.Tests.csproj
  YourApp.UI.Tests/
    YourApp.UI.Tests.csproj
.editorconfig
.gitignore
your-app.sln
```

**Notes**

- Each feature is an **independent project** depending on `YourApp.Core`.
- `YourApp.App` is the **only** executable/composition root.
- `YourApp.Adapters` provides reusable adapter implementations across features.

## Folder/Project Conventions

- **Core**

  - `Adapters/`: contracts for time, file, env, http, etc.
  - `Extensions/`: reusable extension methods (avoid feature leakage).
  - `<Feature>/`: contracts & DTOs per feature (no infra).

- **Feature Projects**

  - Implement Core contracts.
  - Keep **Ports/Adapters** at the edge; domain-facing code stays clean.
  - Optional `Extensions/` for feature-local helpers.

- **Adapters Project (optional)**

  - Houses common adapter implementations reusable across features.

- **Bootstrap/App**
  - DI registration, configuration, pipeline wiring, entry points.
