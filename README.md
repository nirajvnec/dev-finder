// File 1: IEntityServiceFactory.cs
using System.Text.Json;

namespace Marvel.OperationalServices.ApiHost.Services;

public interface IEntityServiceFactory
{
    Task<JsonElement?> GetByIdAsync(string entityName, string id);
    Task<IEnumerable<JsonElement>> GetAllAsync(string entityName);
    Task<JsonElement> AddAsync(string entityName, JsonElement entity);
    Task<JsonElement> UpdateAsync(string entityName, JsonElement entity);
    Task<bool> DeleteAsync(string entityName, string id);
    IEnumerable<string> GetSupportedEntityNames();
}

// File 2: EntityServiceFactory.cs
using System.Text.Json;
using Marvel.OperationalServices.Business.Interfaces;

namespace Marvel.OperationalServices.ApiHost.Services;

public class EntityServiceFactory : IEntityServiceFactory
{
    private readonly IServiceProvider _serviceProvider;
    private readonly IEntityTypeProvider _entityTypeProvider;
    private readonly ILogger<EntityServiceFactory> _logger;

    public EntityServiceFactory(
        IServiceProvider serviceProvider,
        IEntityTypeProvider entityTypeProvider,
        ILogger<EntityServiceFactory> logger)
    {
        _serviceProvider = serviceProvider;
        _entityTypeProvider = entityTypeProvider;
        _logger = logger;
    }

    public async Task<JsonElement?> GetByIdAsync(string entityName, string id)
    {
        ValidateEntityName(entityName);
        ValidateId(id);

        try
        {
            _logger.LogInformation("Getting {EntityName} with ID {Id}", entityName, id);

            var service = GetGenericService(entityName);
            var method = service.GetType().GetMethod("GetByIdAsync");
            
            if (method == null)
                throw new InvalidOperationException($"GetByIdAsync method not found for {entityName}");

            var task = (Task)method.Invoke(service, new object[] { id })!;
            await task;

            var resultProperty = task.GetType().GetProperty("Result");
            var result = resultProperty?.GetValue(task);
            
            if (result == null)
            {
                _logger.LogInformation("{EntityName} with ID {Id} not found", entityName, id);
                return null;
            }

            var json = JsonSerializer.Serialize(result, new JsonSerializerOptions
            {
                PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
                WriteIndented = false
            });
            
            return JsonSerializer.Deserialize<JsonElement>(json);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error getting {EntityName} with ID {Id}", entityName, id);
            throw;
        }
    }

    public async Task<IEnumerable<JsonElement>> GetAllAsync(string entityName)
    {
        ValidateEntityName(entityName);

        try
        {
            _logger.LogInformation("Getting all {EntityName} entities", entityName);

            var service = GetGenericService(entityName);
            var method = service.GetType().GetMethod("GetAllAsync");
            
            if (method == null)
                throw new InvalidOperationException($"GetAllAsync method not found for {entityName}");

            var task = (Task)method.Invoke(service, Array.Empty<object>())!;
            await task;

            var resultProperty = task.GetType().GetProperty("Result");
            var result = (IEnumerable<object>)(resultProperty?.GetValue(task) ?? Array.Empty<object>());

            return result.Select(entity =>
            {
                var json = JsonSerializer.Serialize(entity, new JsonSerializerOptions
                {
                    PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
                    WriteIndented = false
                });
                return JsonSerializer.Deserialize<JsonElement>(json);
            });
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error getting all {EntityName} entities", entityName);
            throw;
        }
    }

    public async Task<JsonElement> AddAsync(string entityName, JsonElement entity)
    {
        ValidateEntityName(entityName);
        ValidateEntity(entity);

        try
        {
            _logger.LogInformation("Adding new {EntityName} entity", entityName);

            var service = GetGenericService(entityName);
            var entityType = _entityTypeProvider.GetEntityType(entityName)!;
            
            var deserializedEntity = JsonSerializer.Deserialize(entity.GetRawText(), entityType);
            
            if (deserializedEntity == null)
                throw new InvalidOperationException($"Failed to deserialize {entityName} entity");

            var method = service.GetType().GetMethod("AddAsync");
            if (method == null)
                throw new InvalidOperationException($"AddAsync method not found for {entityName}");

            var task = (Task)method.Invoke(service, new[] { deserializedEntity })!;
            await task;

            var resultProperty = task.GetType().GetProperty("Result");
            var result = resultProperty?.GetValue(task);

            if (result == null)
                throw new InvalidOperationException($"Failed to add {entityName} entity");

            var json = JsonSerializer.Serialize(result, new JsonSerializerOptions
            {
                PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
                WriteIndented = false
            });
            
            return JsonSerializer.Deserialize<JsonElement>(json);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error adding {EntityName} entity", entityName);
            throw;
        }
    }

    public async Task<JsonElement> UpdateAsync(string entityName, JsonElement entity)
    {
        ValidateEntityName(entityName);
        ValidateEntity(entity);

        try
        {
            _logger.LogInformation("Updating {EntityName} entity", entityName);

            var service = GetGenericService(entityName);
            var entityType = _entityTypeProvider.GetEntityType(entityName)!;
            
            var deserializedEntity = JsonSerializer.Deserialize(entity.GetRawText(), entityType);
            
            if (deserializedEntity == null)
                throw new InvalidOperationException($"Failed to deserialize {entityName} entity");

            var method = service.GetType().GetMethod("UpdateAsync");
            if (method == null)
                throw new InvalidOperationException($"UpdateAsync method not found for {entityName}");

            var task = (Task)method.Invoke(service, new[] { deserializedEntity })!;
            await task;

            var resultProperty = task.GetType().GetProperty("Result");
            var result = resultProperty?.GetValue(task);

            if (result == null)
                throw new InvalidOperationException($"Failed to update {entityName} entity");

            var json = JsonSerializer.Serialize(result, new JsonSerializerOptions
            {
                PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
                WriteIndented = false
            });
            
            return JsonSerializer.Deserialize<JsonElement>(json);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error updating {EntityName} entity", entityName);
            throw;
        }
    }

    public async Task<bool> DeleteAsync(string entityName, string id)
    {
        ValidateEntityName(entityName);
        ValidateId(id);

        try
        {
            _logger.LogInformation("Deleting {EntityName} with ID {Id}", entityName, id);

            var service = GetGenericService(entityName);
            var method = service.GetType().GetMethod("DeleteAsync");
            
            if (method == null)
                throw new InvalidOperationException($"DeleteAsync method not found for {entityName}");

            var task = (Task)method.Invoke(service, new object[] { id })!;
            await task;

            var resultProperty = task.GetType().GetProperty("Result");
            var result = resultProperty?.GetValue(task);

            return result is bool success && success;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error deleting {EntityName} with ID {Id}", entityName, id);
            throw;
        }
    }

    public IEnumerable<string> GetSupportedEntityNames()
    {
        return _entityTypeProvider.GetRegisteredEntityNames();
    }

    private object GetGenericService(string entityName)
    {
        var entityType = _entityTypeProvider.GetEntityType(entityName);
        if (entityType == null)
            throw new ArgumentException($"Unknown entity type: {entityName}");

        var keyType = _entityTypeProvider.GetKeyType(entityName);
        var serviceType = typeof(IGenericService<,>).MakeGenericType(entityType, keyType);
        
        var service = _serviceProvider.GetRequiredService(serviceType);
        if (service == null)
            throw new InvalidOperationException($"Service not registered for {entityName}");

        return service;
    }

    private void ValidateEntityName(string entityName)
    {
        if (string.IsNullOrWhiteSpace(entityName))
            throw new ArgumentException("Entity name cannot be null or empty", nameof(entityName));

        if (_entityTypeProvider.GetEntityType(entityName) == null)
        {
            var supportedNames = string.Join(", ", _entityTypeProvider.GetRegisteredEntityNames());
            throw new ArgumentException(
                $"Entity type '{entityName}' is not supported. Supported types: {supportedNames}",
                nameof(entityName));
        }
    }

    private static void ValidateId(string id)
    {
        if (string.IsNullOrWhiteSpace(id))
            throw new ArgumentException("ID cannot be null or empty", nameof(id));
    }

    private static void ValidateEntity(JsonElement entity)
    {
        if (entity.ValueKind == JsonValueKind.Null || entity.ValueKind == JsonValueKind.Undefined)
            throw new ArgumentException("Entity cannot be null or undefined", nameof(entity));
    }
}