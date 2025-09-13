# Error Handling

Error handling is about **making failures explicit** and **keeping control flow predictable**.  
Exceptions are powerful but should be reserved for _exceptional situations_ — not for domain logic or expected outcomes.

## General Rules

- ❌ **Don’t** use exceptions for domain logic or normal control flow.
- ✅ Use exceptions to **assert code contracts** (e.g., `ArgumentNullException`).
- ✅ Use exceptions only when an error **cannot be recovered gracefully** (e.g., missing configuration, failing database connection).
- ✅ Prefer **built-in exception types** over custom ones, unless a custom exception adds clear value.
- ⚠️ `TryParse`-style methods (`bool TryParse(out int value)`) are useful for **simple cases**, but they:
  - Do not provide detailed error messages.
  - Cannot be easily extended to async scenarios.

## Using Exceptions Correctly

```csharp
// ✅ Argument validation
public void Connect(string connectionString)
{
    if (connectionString is null)
    {
        throw new ArgumentNullException(nameof(connectionString));
    }

    // connect ...
}

// ✅ Exceptional case: unrecoverable failure
if (!File.Exists(configPath))
{
    throw new FileNotFoundException("Configuration file is missing.", configPath);
}
```

## Domain Errors with Discriminated Unions

For **domain errors** (expected outcomes that are not "exceptions"), use discriminated unions or result types instead of throwing exceptions.  
This makes error handling **explicit and type-safe**.

```csharp
public interface ISerializerResult<T>
{
    public sealed record Success(T Configuration) : ISerializerResult<T>;
    public sealed record Error(string Message) : ISerializerResult<T>;
}

// ✅ Usage
void LoadConfiguration()
{
	ISerializerResult<MyConfiguration> result = Deserialize(configJson);
	MyConfiguration? config = result switch
	{
			ISerializerResult<MyConfiguration>.Success(var cfg) => cfg,
			ISerializerResult<MyConfiguration>.Error(var message) => HandleError(message)
	};

	return config;

	static MyConfiguration? HandleError(string message)
	{
		logger.Log(message);
		return default;
	}
}
```

## Summary

- ✅ Use exceptions for **unexpected and unrecoverable errors**.
- ✅ Validate arguments and throw `ArgumentNullException` / `ArgumentOutOfRangeException`.
- ✅ Prefer built-in exceptions over custom ones.
- ❌ Don’t use exceptions for **domain logic** — model those cases explicitly with result types.
- ⚠️ Use `TryParse`-style APIs only for **simple, performance-sensitive scenarios**.
