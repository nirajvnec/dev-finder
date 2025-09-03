// File 1: IGenericRepository.cs
namespace Marvel.DataHub.Repositories.Interfaces;

public interface IGenericRepository<TEntity, TKey> where TEntity : class
{
    Task<TEntity?> GetByIdAsync(TKey id);
    Task<IEnumerable<TEntity>> GetAllAsync();
    Task<TEntity> AddAsync(TEntity entity);
    Task<TEntity> UpdateAsync(TEntity entity);
    Task<bool> DeleteAsync(TKey id);
}

// File 2: GenericRepository.cs
using Marvel.DataHub.Repositories.Interfaces;
using Marvel.DataHub.Repositories.Sql;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Logging;

namespace Marvel.DataHub.Repositories.Implementations;

public class GenericRepository<TEntity, TKey> : IGenericRepository<TEntity, TKey> 
    where TEntity : class
{
    protected readonly MarvelDbContext _context;
    protected readonly DbSet<TEntity> _dbSet;
    protected readonly ILogger<GenericRepository<TEntity, TKey>> _logger;

    public GenericRepository(MarvelDbContext context, ILogger<GenericRepository<TEntity, TKey>> logger)
    {
        _context = context;
        _dbSet = _context.Set<TEntity>();
        _logger = logger;
    }

    public virtual async Task<TEntity?> GetByIdAsync(TKey id)
    {
        try
        {
            _logger.LogInformation("Getting {EntityType} with ID {Id}", typeof(TEntity).Name, id);
            return await _dbSet.FindAsync(id);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error getting {EntityType} with ID {Id}", typeof(TEntity).Name, id);
            throw;
        }
    }

    public virtual async Task<IEnumerable<TEntity>> GetAllAsync()
    {
        try
        {
            _logger.LogInformation("Getting all {EntityType} entities", typeof(TEntity).Name);
            return await _dbSet.ToListAsync();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error getting all {EntityType} entities", typeof(TEntity).Name);
            throw;
        }
    }

    public virtual async Task<TEntity> AddAsync(TEntity entity)
    {
        try
        {
            _logger.LogInformation("Adding new {EntityType} entity", typeof(TEntity).Name);
            var result = await _dbSet.AddAsync(entity);
            await _context.SaveChangesAsync();
            return result.Entity;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error adding {EntityType} entity", typeof(TEntity).Name);
            throw;
        }
    }

    public virtual async Task<TEntity> UpdateAsync(TEntity entity)
    {
        try
        {
            _logger.LogInformation("Updating {EntityType} entity", typeof(TEntity).Name);
            _dbSet.Update(entity);
            await _context.SaveChangesAsync();
            return entity;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error updating {EntityType} entity", typeof(TEntity).Name);
            throw;
        }
    }

    public virtual async Task<bool> DeleteAsync(TKey id)
    {
        try
        {
            _logger.LogInformation("Deleting {EntityType} with ID {Id}", typeof(TEntity).Name, id);
            var entity = await GetByIdAsync(id);
            if (entity == null) 
                return false;

            _dbSet.Remove(entity);
            await _context.SaveChangesAsync();
            return true;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error deleting {EntityType} with ID {Id}", typeof(TEntity).Name, id);
            throw;
        }
    }
}