// File 1: IEntityTypeProvider.cs
namespace Marvel.OperationalServices.Business.Interfaces;

public interface IEntityTypeProvider
{
    Type? GetEntityType(string entityName);
    IEnumerable<string> GetRegisteredEntityNames();
    Type GetKeyType(string entityName);
}

// File 2: EntityTypeProvider.cs
using Marvel.OperationalServices.Business.Interfaces;
using Marvel.DataHub.Entities;

namespace Marvel.OperationalServices.Business.Services;

public class EntityTypeProvider : IEntityTypeProvider
{
    private readonly Dictionary<string, (Type EntityType, Type KeyType)> _entityTypes;

    public EntityTypeProvider()
    {
        _entityTypes = new Dictionary<string, (Type EntityType, Type KeyType)>(StringComparer.OrdinalIgnoreCase)
        {
            { "product", (typeof(Product), typeof(string)) },
            { "car", (typeof(Car), typeof(string)) },
            { "vehicle", (typeof(Vehicle), typeof(string)) },
            { "football", (typeof(Football), typeof(string)) }
        };
    }

    public Type? GetEntityType(string entityName)
    {
        return _entityTypes.TryGetValue(entityName, out var types) ? types.EntityType : null;
    }

    public Type GetKeyType(string entityName)
    {
        return _entityTypes.TryGetValue(entityName, out var types) ? types.KeyType : typeof(string);
    }

    public IEnumerable<string> GetRegisteredEntityNames()
    {
        return _entityTypes.Keys;
    }
}