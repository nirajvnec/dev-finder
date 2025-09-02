using System.Text.Json;
using System.Threading.Tasks;
using System.Collections.Generic;

namespace Marvel.OperationalServices.Services.Contracts
{
    public interface IGenericService
    {
        Task<object> AddAsync(string entityName, JsonElement jsonData);
        Task<object> UpdateAsync(string entityName, object id, JsonElement jsonData);
        Task<bool> DeleteAsync(string entityName, object id);
        Task<object?> GetByIdAsync(string entityName, object id);
        Task<IEnumerable<object>> GetAllAsync(string entityName);
    }
}