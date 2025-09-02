using System.Collections.Generic;
using System.Threading.Tasks;

namespace Marvel.OperationalServices.Repositories
{
    public interface IRepository
    {
        Task<object?> GetByIdAsync(object id);
        Task<IEnumerable<object>> GetAllAsync();
        Task<object> AddAsync(object entity);
        Task<object?> UpdateAsync(object id, object entity);
        Task<bool> DeleteAsync(object id);
    }
}