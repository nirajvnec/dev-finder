using Microsoft.EntityFrameworkCore;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace Marvel.OperationalServices.Repositories
{
    public class Repository : IRepository
    {
        private readonly DbContext _dbContext;
        private readonly Type _entityType;

        public Repository(DbContext dbContext, Type entityType)
        {
            _dbContext = dbContext;
            _entityType = entityType;
        }

        public async Task<object?> GetByIdAsync(object id)
        {
            return await _dbContext.FindAsync(_entityType, id);
        }

        public async Task<IEnumerable<object>> GetAllAsync()
        {
            var dbSet = _dbContext.Set(_entityType);
            return await Task.FromResult(dbSet.AsEnumerable());
        }

        public async Task<object> AddAsync(object entity)
        {
            var dbSet = _dbContext.Set(_entityType);
            await dbSet.AddAsync(entity);
            await _dbContext.SaveChangesAsync();
            return entity;
        }

        public async Task<object?> UpdateAsync(object id, object entity)
        {
            var dbSet = _dbContext.Set(_entityType);
            var existing = await _dbContext.FindAsync(_entityType, id);
            if (existing == null) return null;

            _dbContext.Entry(existing).CurrentValues.SetValues(entity);
            await _dbContext.SaveChangesAsync();
            return existing;
        }

        public async Task<bool> DeleteAsync(object id)
        {
            var existing = await _dbContext.FindAsync(_entityType, id);
            if (existing == null) return false;

            _dbContext.Remove(existing);
            await _dbContext.SaveChangesAsync();
            return true;
        }
    }
}