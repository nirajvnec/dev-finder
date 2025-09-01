public interface IRepositoryFactory
{
    IRepository<TEntity> CreateRepository<TEntity>() where TEntity : class;
    object CreateRepositoryByType(Type entityType);
}