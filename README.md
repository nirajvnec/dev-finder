using System.Text.Json;
using System.Threading.Tasks;

public interface IGenericService
{
    Task<object?> AddAsync(string entityName, JsonElement data);
    Task<object?> UpdateAsync(string entityName, JsonElement data);
    Task<bool> DeleteAsync(string entityName, int id);
    Task<object?> GetByIdAsync(string entityName, int id);
    Task<IEnumerable<object?>> GetAllAsync(string entityName);
}