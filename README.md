using System.Text.Json;

namespace Marvel.OperationalServices.Factory
{
    public interface IServiceFactory
    {
        object GetService(string entityName);

        Task<object> CreateEntityAsync(string entityName, JsonElement jsonData);
    }
}