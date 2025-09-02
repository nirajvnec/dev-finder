using Marvel.OperationalServices.RepositoriesContract;
using Microsoft.EntityFrameworkCore;
using System;

namespace Marvel.OperationalServices.Factories
{
    public class RepositoryFactory : IRepositoryFactory
    {
        private readonly DbContext _context;

        public RepositoryFactory(DbContext context)
        {
            _context = context;
        }

        public IRepository<TEntity> CreateRepository<TEntity>() where TEntity : class
        {
            return new Repository<TEntity>(_context);
        }

        public IRepository<object> CreateRepositoryByType(Type entityType)
        {
            // dynamically build generic Repository<T>
            var repoType = typeof(Repository<>).MakeGenericType(entityType);

            // Activator will resolve constructor injection with DbContext
            var repository = Activator.CreateInstance(repoType, _context);

            return (IRepository<object>)repository!;
        }
    }
}