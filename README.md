// File 1: IGenericService.cs
namespace Marvel.OperationalServices.Business.Interfaces;

public interface IGenericService<TEntity, TKey> where TEntity : class
{
    Task<TEntity?> GetByIdAsync(TKey id);
    Task<IEnumerable<TEntity>> GetAllAsync();
    Task<TEntity> AddAsync(TEntity entity);
    Task<TEntity> UpdateAsync(TEntity entity);
    Task<bool> DeleteAsync(TKey id);
}

// File 2: GenericService.cs
using Marvel.OperationalServices.Business.Interfaces;
using Marvel.DataHub.Repositories.Interfaces;
using Microsoft.Extensions.Logging;

namespace Marvel.OperationalServices.Business.Services;

public class GenericService<TEntity, TKey> : IGenericService<TEntity, TKey> 
    where TEntity : class
{
    protected readonly IGenericRepository<TEntity, TKey> _repository;
    protected readonly ILogger<GenericService<TEntity, TKey>> _logger;

    public GenericService(
        IGenericRepository<TEntity, TKey> repository,
        ILogger<GenericService<TEntity, TKey>> logger)
    {
        _repository = repository;
        _logger = logger;
    }

    public virtual async Task<TEntity?> GetByIdAsync(TKey id)
    {
        if (id == null)
            throw new ArgumentNullException(nameof(id));

        _logger.LogInformation("Service: Getting {EntityType} with ID {Id}", typeof(TEntity).Name, id);
        return await _repository.GetByIdAsync(id);
    }

    public virtual async Task<IEnumerable<TEntity>> GetAllAsync()
    {
        _logger.LogInformation("Service: Getting all {EntityType} entities", typeof(TEntity).Name);
        return await _repository.GetAllAsync();
    }

    public virtual async Task<TEntity> AddAsync(TEntity entity)
    {
        if (entity == null)
            throw new ArgumentNullException(nameof(entity));

        _logger.LogInformation("Service: Adding new {EntityType} entity", typeof(TEntity).Name);
        return await _repository.AddAsync(entity);
    }

    public virtual async Task<TEntity> UpdateAsync(TEntity entity)
    {
        if (entity == null)
            throw new ArgumentNullException(nameof(entity));

        _logger.LogInformation("Service: Updating {EntityType} entity", typeof(TEntity).Name);
        return await _repository.UpdateAsync(entity);
    }

    public virtual async Task<bool> DeleteAsync(TKey id)
    {
        if (id == null)
            throw new ArgumentNullException(nameof(id));

        _logger.LogInformation("Service: Deleting {EntityType} with ID {Id}", typeof(TEntity).Name, id);
        return await _repository.DeleteAsync(id);
    }
}