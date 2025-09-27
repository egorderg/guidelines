# Workflow

A **Workflow** is an orchestration pattern.  
It doesn’t contain business rules itself but **combines multiple steps** (use cases/operations) and executes them in sequence (or conditionally).

## Naming

- A workflow _can_ use the suffix `Workflow`, e.g. `SetupProjectWorkflow`.
- The suffix is **not required**: a workflow might appear as:
  - An API request handler
  - A CLI command handler
  - A higher-level operation/use case

## Characteristics

- A workflow may itself be defined as a **use case/operation** that internally calls other use cases/operations.
- It is responsible only for **orchestration** — the order and combination of steps.
- Each step should remain **independent and reusable**.
- They **simplify complex business operations** by combining simpler operations into one clear flow.

## Example

```csharp
// Two simple operations
public interface ICreateProjectOperation
{
	Task<Guid> HandleAsync(string name, CancellationToken ct = default);
}

public interface IInitializeRepositoryOperation
{
	Task HandleAsync(Guid projectId, CancellationToken ct = default);
}

// Workflow that composes both
public sealed class SetupProjectWorkflow(ICreateProjectOperation create, IInitializeRepositoryOperation init)
{
	public async Task<Guid> RunAsync(string name, CancellationToken ct = default)
	{
		var projectId = await create.HandleAsync(name, ct);
		await init.HandleAsync(projectId, ct);
		return projectId;
	}
}
```
