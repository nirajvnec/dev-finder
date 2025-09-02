using System;

namespace Marvel.OperationalServices.Factories
{
    public interface IRepositoryFactory
    {
        IRepository CreateRepositoryByType(Type entityType);
    }
}