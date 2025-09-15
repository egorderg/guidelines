# Gateway & Adapter Architecture

This architecture is inspired by Hexagonal Architecture.  
It emphasizes **clarity** and **practicality** while keeping the **domain pure** and **technology replaceable**.

## Philosophy

- ✅ **Core** is stable, long-lived, and independent of frameworks or technology choices.
- ✅ **Application** defines operations, workflows, and policies to operate on the Core.
- ✅ **Adapters** are always outbound and replaceable (databases, renderers, external APIs, messaging, storage, …).
- ✅ **Presentation** provides the startup (Web, CLI, Lambda, Jobs) and simply calls the gateways into Application.
- ⚠️ Don’t follow patterns dogmatically — apply them **practically** depending on the use case.

## Adapters & Gateways

> The terms **Adapters** and **Gateways** are conceptual. They describe directions of communication, not literal class names that must be used in code.

- **Adapters**: define the services the **Application** requires from the outside world.

  - Examples: persistence, messaging, rendering, storage.
  - Implemented in Infrastructure, replaceable without changing Core or Application.

- **Gateways**: define the operations the **outside world** can perform on the domain (through Application).

  - Examples: `IChangeName`, `IEngine`, `IUserInterface`.
  - Implemented by Application, called by Presentation (Web, CLI, Lambda).

## Layers

### Core:

- **Pure**: contains only business/domain concepts.
  - No dependencies on frameworks, databases, HTTP, UI, etc.
- **Domain services**: logic that only applies to the domain.
- **No references** to _Application_ or _Adapters_.
- **No implementation details** about how the system runs or persists.

#### Examples

- Plain objects: `Player`, `GameSession`, `Vector2`
- Domain contracts: `INode`, `IDrawable`
- Domain service: `DamageCalculator`

### Application

- **Depends only on Core.**
- Defines **operations, policies, and workflows**: the gateways to operate on the Core.
- Exposes **contracts** that external systems (Presentation, CLI, Lambda, etc.) can call to invoke this business logic.
- Declares **adapter contracts** required to perform these operations  
  (e.g., `IRenderer`, `ISaveGameRepository`), but does not implement them.
- **Agnostic to frameworks** — no direct dependency on ASP.NET Core, EF, MediatR, etc.
- **Extendable with artifacts** — Application can be split into feature-specific modules (e.g. `YourApp.Application.Todo`, `YourApp.Application.Admin`) with its own gateways.
- **Implements orchestration styles**, such as:
  - **CQRS** (commands/queries)
  - **Workflow pattern** (multi-step business flows)
  - **Service pattern** (application services calling domain logic)
  - **Custom orchestrations**, provided each class follows **SRP**

### Adapters

- Implement the contracts defined in the **Application** layer.
- Map between external representations and **Core** objects.
- Handle technical concerns such as persistence, messaging, file I/O, rendering, etc.
- Provide the **bridge** between the domain and the outside world.
- Isolated from **Core** and **Application**, so they can be swapped or replaced without changing business logic.

#### Examples

- `EfCoreSaveGameRepository` (implements `ISaveGameRepository`)
- `RabbitMqEventPublisher` (implements `IEventPublisher`)
- `SkiaRenderer` (implements `IRenderer`)

## Presentation

- The **only place** where all layers meet.
- Provides the actual **entry point** (`Program.cs`, CLI command, Lambda handler, etc.).
- Wires everything together using **Dependency Injection (DI)**.

### Responsibilities

- Bind **adapters** to their implementations (Infrastructure).
- Bind **gateways** to their implementations (Application).
- Configure the runtime (e.g., web host, CLI runtime, game engine).
- Call **gateways** to drive application behavior.

## Example Folder Structure

```text
YourApp/
├─ YourApp.sln
├─ .editorconfig
├─ .gitignore
├─ docs/
│  └─ architecture.md
└─ src/
   ├─ YourApp.Core/                         # Pure domain layer
   │  ├─ Exceptions/                        # General domain exceptions
	 │  ├─ Rendering/
	 │  │	 ├─ Material.cs
	 │  │	 └─ Shader.cs
	 │  ├─ Scene.cs
	 │  ├─ Game.cs
	 │  ├─ INode.cs
   │  └─ YourApp.Core.csproj
   │
   ├─ YourApp.Application/                  # Implements operations
   │  ├─ Adapters/													# Adapter contracts
	 │  │	 ├─ IRenderingBackend.cs
	 │  │	 ├─ IInputSystem.cs
	 │  │	 └─ IFileSystem.cs
   │  ├─ IEngine.cs													# Gateway
   │  ├─ Engine.cs
   │  └─ YourApp.Application.csproj
   │
   ├─ YourApp.Application.UserInterface/    # Implements ui operations and gateways
   │  ├─ IUserInterface.cs
   │  ├─ UserInterface.cs
   │  └─ YourApp.Application.csproj
   │
   ├─ YourApp.Adapters.Rendering.OpenGL/    # Adapter (e.g., OpenGL)
   │  └─ YourApp.Adapters.Rendering.csproj
   ├─ YourApp.Adapters.Storage.FileSystem/  # Adapter (e.g., FileSystem persistence)
   │  └─ YourApp.Adapters.Storage.FileSystem.csproj
   ├─ YourApp.Adapters.Input/               # Adapter (e.g., Input service)
   │  └─ YourApp.Adapters.Input.csproj
   │
   ├─ YourApp.Presentation.Cli/             # CLI entry point
   │  ├─ Program.cs
   │  └─ YourApp.Presentation.Cli.csproj
   ├─ YourApp.Presentation.Runtime/         # Runtime entry point
   │  ├─ Program.cs
   │  └─ YourApp.Presentation.Runtime.csproj
   ├─ YourApp.Presentation.Editor/          # Editor entry point
   │  ├─ Program.cs
   │  └─ YourApp.Presentation.Editor.csproj
   │
└─ tests/
   ├─ YourApp.Core.Tests/
   │  └─ YourApp.Core.Tests.csproj
   ├─ YourApp.Application.Tests/
   │  └─ YourApp.Application.Tests.csproj
   └─ YourApp.Adapters.Rendering.Tests/
      └─ YourApp.Adapters.Rendering.Tests.csproj
```

## Summary

- ✅ **Core**: pure domain — entities and small domain services.
- ✅ **Application**: defines business operations (Gateways) and required external services (Adapters), implements workflows and policies.
- ✅ **Adapters**: implement the contracts defined in Application and wrap infrastructure (databases, messaging, rendering, storage).
- ✅ **Presentation**: the composition root — wires everything together with DI, configures the runtime, and drives application behavior.
- ⚠️ Don’t couple the domain to frameworks or infrastructure — **Core must outlive tech choices**.

## Graphic

![Graphic](./gateway-adapter.drawio.svg)

## FAQ

### Why put the contracts for **Adapters** and **Gateways** into **Application** instead of **Core**?

Because they are an **implementation detail**.  
Core only defines objects and small services that operate on those objects.  
Application defines how the system operates internally and how it communicates externally.

Referencing Application in Presentation just to access the Gateway contracts is fine, since those contracts belong to the Application’s definition of its workflows.  
If contracts were generalized in Core, it would limit how the Application can evolve or allow alternative implementations.

### What is the difference between this and the **Onion Architecture**?

Onion Architecture lets the outside communicate directly with the Application layer.  
In this architecture, the **Application core is isolated** and communicates with the outside world only through **Gateways** (inbound) and **Adapters** (outbound).  
This makes the boundaries clearer and the core easier to evolve or replace.

### What goes in **Core** vs **Application**

- **Core**: defines domain concepts.

  - It does **not** define how the system runs.
  - Contracts/interfaces here are only for **external usage** (e.g. plugin API or Presentation).
  - Keep it small.

- **Application**: defines how the domain runs using those concepts.
  - Gateways and Adapters live here, since they are **orchestration contracts**.

**Rule of Thumb:**

- If a contract from Core is implemented in Application, it’s in the **wrong place**.
- If a object is only needed by an Adapter → it belongs in **Application**, not in Core.

### Why not mix **Core** and **Application**?

Blending Core and Application blurs two different concerns:

- **Core** = domain concepts (what the system _is_).
- **Application** = operations/workflows (what the system _does_ with the Core).

If you mix them, you end up with folders that look separated, but it’s harder to tell **what is domain** vs. **what is orchestration**. Keeping them apart lets you:

- reuse the **Core** across different presentations (Web, CLI, Editor, Lambda),
- evolve or replace the **Application** (operations) without touching domain truth,
- reason about changes: “did we change the concept or just how we operate it?”

#### Example 1 — Engine & Nodes

- **Core** defines `Node` and related domain types.
- **Application** defines Gateways/operations to manipulate nodes (load scene, update, render pass, etc.).
- You can swap to a faster engine implementation or change workflows by modifying **Application** only.  
  The **Core** concept stays intact; only **Gateways** change.

#### Example 2 — API and CLI

- Both use the same **Core** concepts.
- **Application** provides the Gateways (operations) they call.
- The CLI can use an **extended Application** (extra Gateways like YourApp.Application.Admin) without affecting the API.  
  Same domain concept, different operations surface.
