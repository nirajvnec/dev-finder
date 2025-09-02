using System;
using System.Collections.Generic;
using System.Linq;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.EntityFrameworkCore;
using Marvel.OperationalServices.RepositoriesContract;
using Marvel.OperationalServices.Helpers;

namespace Marvel.OperationalServices.Repositories
{
    public class Repository : IRepository
    {
        private readonly DbContext _dbContext;

        public Repository(DbContext dbContext)
        {
            _dbContext = dbContext;
        }

        public async Task<object> AddAsync(string entityName, JsonElement json)
        {
            var entityType = ReflectionHelper.GetEntityType(entityName);
            var entity = JsonSerializer.Deserialize(json.GetRawText(), entityType);

            _dbContext.Add(entity!);
            await _dbContext.SaveChangesAsync();

            return entity!;
        }

        public async Task<object?> GetByIdAsync(string entityName, object id)
        {
            var entityType = ReflectionHelper.GetEntityType(entityName);
            return await _dbContext.FindAsync(entityType, id);
        }

        public async Task<IEnumerable<object>> GetAllAsync(string entityName)
        {
            var entityType = ReflectionHelper.GetEntityType(entityName);
            var set = _dbContext.Set(entityType); // non-generic
            return await Task.FromResult(set.AsEnumerable());
        }

        public async Task<object> UpdateAsync(string entityName, JsonElement json)
        {
            var entityType = ReflectionHelper.GetEntityType(entityName);
            var entity = JsonSerializer.Deserialize(json.GetRawText(), entityType);

            _dbContext.Update(entity!);
            await _dbContext.SaveChangesAsync();

            return entity!;
        }

        public async Task DeleteAsync(string entityName, object id)
        {
            var entityType = ReflectionHelper.GetEntityType(entityName);
            var entity = await _dbContext.FindAsync(entityType, id);

            if (entity == null) throw new KeyNotFoundException($"{entityName} with ID {id} not found");

            _dbContext.Remove(entity);
            await _dbContext.SaveChangesAsync();
        }
    }
}