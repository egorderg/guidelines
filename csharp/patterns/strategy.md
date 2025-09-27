# Strategy

The **Strategy pattern** abstracts specific logic into interchangeable behaviors.  
A handler/engine works against a **common contract**, while concrete strategies implement the actual logic.

## Naming

- A strategy contract can use the suffix `Strategy`, e.g. `IAuthenticationStrategy`.
- The suffix is **not required** if the concept is already clear, e.g. `IParser`, `IAuthenticator`.
- Concrete strategies should be named by their variant, e.g. `JsonParser`, `YamlParser`, `TokenAuthenticator`.

## Characteristics

- Define a **contract** (interface or abstract class).
- Provide **different implementations** for the same task.
- Use a **common class** (context/handler/engine) that operates only on the contract.
- Makes it easy to **swap, extend, or test** specific behaviors without changing the core logic.

## Example

```csharp
// Contract (strategy)
public interface IConfigSerializer
{
    T Deserialize<T>(string content);
    string Serialize<T>(T obj);
}

// Concrete strategies
public sealed class JsonConfigSerializer : IConfigSerializer
{
    public T Deserialize<T>(string content) =>
        JsonSerializer.Deserialize<T>(content)!;

    public string Serialize<T>(T obj) =>
        JsonSerializer.Serialize(obj, new JsonSerializerOptions { WriteIndented = true });
}

public sealed class YamlConfigSerializer : IConfigSerializer
{
    public T Deserialize<T>(string content) =>
        new DeserializerBuilder().Build().Deserialize<T>(content);

    public string Serialize<T>(T obj) =>
        new SerializerBuilder().Build().Serialize(obj);
}

// Handler/engine using the strategy
public sealed class ConfigParser(IConfigSerializer serializer)
{
    public T Load<T>(string path) =>
        serializer.Deserialize<T>(File.ReadAllText(path));

    public void Save<T>(string path, T obj) =>
        File.WriteAllText(path, serializer.Serialize(obj));
}

```
