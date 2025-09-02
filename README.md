using Marvel.OperationalServices.RepositoriesContract;
using Microsoft.EntityFrameworkCore;

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
    }
}