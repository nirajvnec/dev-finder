using System;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Marvel.OperationalServices.Service;
using Marvel.OperationalServices.Factory.Helpers; // if you have ReflectionHelper here

namespace Marvel.OperationalServices.Factory
{
    public class ServiceFactory : IServiceFactory
    {
        private readonly IServiceProvider _serviceProvider;

        public ServiceFactory(IServiceProvider serviceProvider)
        {
            _serviceProvider = serviceProvider;
        }

        public object GetService(string entityName)
        {
            // Resolve entity type dynamically
            var entityType = ReflectionHelper.GetEntityType(entityName);
            if (entityType == null)
                throw new InvalidOperationException($"Entity type '{entityName}' not found.");

            // Construct GenericService<TEntity>
            var serviceType = typeof(IGenericService<>).MakeGenericType(entityType);

            var service = _serviceProvider.GetService(serviceType);
            if (service == null)
                throw new InvalidOperationException($"Service for '{entityName}' not registered.");

            return service;
        }

        public async Task<object> CreateEntityAsync(string entityName, JsonElement jsonData)
        {
            var entityType = ReflectionHelper.GetEntityType(entityName);
            if (entityType == null)
                throw new InvalidOperationException($"Entity type '{entityName}' not found.");

            // Deserialize JSON into entity object
            var entity = JsonSerializer.Deserialize(jsonData.GetRawText(), entityType);
            if (entity == null)
                throw new InvalidOperationException($"Failed to deserialize JSON to '{entityName}'.");

            // Resolve service
            var serviceType = typeof(IGenericService<>).MakeGenericType(entityType);
            var service = _serviceProvider.GetService(serviceType);

            if (service == null)
                throw new InvalidOperationException($"Service for '{entityName}' not registered.");

            // Call GenericService<TEntity>.AddAsync(entity)
            var method = serviceType.GetMethod("AddAsync");
            if (method == null)
                throw new InvalidOperationException($"AddAsync method not found on service '{serviceType.Name}'.");

            var task = (Task)method.Invoke(service, new[] { entity })!;
            await task.ConfigureAwait(false);

            // Get Task.Result via reflection
            var resultProperty = task.GetType().GetProperty("Result");
            return resultProperty!.GetValue(task)!;
        }
    }
}