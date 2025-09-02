public interface IRepositoryFactory
{
    IRepository<T> CreateRepository<T>() where T : class, new();
}

public class RepositoryFactory : IRepositoryFactory
{
    public IRepository<T> CreateRepository<T>() where T : class, new()
    {
        return new Repository<T>();
    }
}
