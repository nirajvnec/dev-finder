using System.Collections.Generic;
using System.Text.Json;
using System.Threading.Tasks;

namespace Marvel.OperationalServices.Service.Contracts
{
    /// <summary>
    /// Defines a generic contract for service operations across all entities.
    /// </summary>
    /// <typeparam name="TEntity">Entity type</typeparam>
    public interface IGenericService<TEntity> where TEntity : class
    {
        /// <summary>
        /// Retrieves all entities.
        /// </summary>
        Task<IEnumerable<TEntity>> GetAllAsync();

        /// <summary>
        /// Retrieves an entity by its primary key.
        /// </summary>
        Task<TEntity?> GetByIdAsync(object id);

        /// <summary>
        /// Creates a new entity from JSON input.
        /// </summary>
        Task<TEntity> CreateAsync(JsonElement jsonData);

        /// <summary>
        /// Updates an existing entity by id with JSON data.
        /// </summary>
        Task<TEntity?> UpdateAsync(object id, JsonElement jsonData);

        /// <summary>
        /// Deletes an entity by id.
        /// </summary>
        Task<bool> DeleteAsync(object id);
    }
}