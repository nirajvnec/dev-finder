using Microsoft.EntityFrameworkCore;
using System;

public class RepositoryFactory : IRepositoryFactory
{
    private readonly DbContext _dbContext;

    public RepositoryFactory(DbContext dbContext)
    {
        _dbContext = dbContext;
    }

    public IRepository<TEntity> CreateRepository<TEntity>() where TEntity : class
    {
        return new GenericRepository<TEntity>(_dbContext);
    }

    public object CreateRepositoryByType(Type entityType)
    {
        var repoType = typeof(GenericRepository<>).MakeGenericType(entityType);
        return Activator.CreateInstance(repoType, _dbContext)!;
    }
}