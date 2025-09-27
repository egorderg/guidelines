# Use Case / Operation

This project organizes application logic into small, single-purpose classes.  
Each class represents **one business action** (create, update, query, etc.) and follows the **Use Case pattern**.

## Naming Convention

- Classes may be suffixed with either **`UseCase`** or **`Operation`**.
  - Both are acceptable, but **Operation** is preferred when the focus is on the business core.
  - **UseCase** can be used when aligning with Clean Architecture terminology.

## General Rules

1. **Verb + Noun + Suffix**

   - Always start with a verb that reflects the action: `Create`, `Update`, `Delete`, `Load`, `Find`, `List`, `Query`, `Search`.
   - Follow with the domain noun in singular or plural (`Project`, `Projects`).
   - End with either `Operation` or `UseCase`.

2. **Singular vs. Plural**

   - Use **singular** for one entity: `LoadProjectOperation`.
   - Use **plural** for collections: `ListProjectsOperation`.

3. **Consistency Over Variety**
   - Pick either `Operation` or `UseCase` as your default suffix and use it everywhere.
   - Only mix them intentionally (e.g., `Operation` for business-core actions, `UseCase` for orchestration-level logic).

## Examples

### Write Operations (mutations)

- `CreateProjectOperation` / `CreateProjectUseCase`
- `UpdateProjectOperation` / `UpdateProjectUseCase`
- `DeleteProjectOperation` / `DeleteProjectUseCase`

### Read Operations (queries)

- `LoadProjectOperation` / `LoadProjectUseCase` → read one entity
- `FindProjectOperation` / `FindProjectUseCase` → read one by search/filter
- `ListProjectsOperation` / `ListProjectsUseCase` → read multiple entities (all or filtered)
- `QueryProjectsOperation` / `QueryProjectsUseCase` → read multiple with complex filtering or parameters

### Specialized Queries (optional)

- `SearchProjectsOperation` / `SearchProjectsUseCase` → keyword or fuzzy search
- `BrowseProjectsOperation` / `BrowseProjectsUseCase` → paginated or categorized listing

### Code

```cs
// Define the generic operation interface
public interface IOperation<in TRequest, TResponse>
{
  Task<TResponse> Handle(TRequest request, CancellationToken ct = default);
}

// Request/Response DTOs
public sealed record CreateProjectRequest(string Name, string Path);
public sealed record CreateProjectResult(bool Success);

// Example operation implementation
public sealed class CreateProjectOperation(IFileSystem fileSystem) : IOperation<CreateProjectRequest, CreateProjectResult>
{
	public async Task<CreateProjectResult> Handle(CreateProjectRequest request, CancellationToken ct = default)
	{
		if (fileSystem.Directory.Exists(request.Path))
			return new(false);

		fileSystem.Directory.CreateDirectory(request.Path);
		await fileSystem.File.WriteAllTextAsync(
			Path.Combine(request.Path, "project.json"),
			$"{{ \"name\": \"{request.Name}\" }}",
			ct);

		return new(true);
	}
}

```
