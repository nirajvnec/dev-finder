using System;
using System.Linq;
using System.Reflection;

namespace Marvel.OperationalServices.Common
{
    public static class ReflectionHelper
    {
        public static Type? GetEntityType(string entityName, Assembly entitiesAssembly)
        {
            return entitiesAssembly
                .GetTypes()
                .FirstOrDefault(t => t.Name.Equals(entityName, StringComparison.OrdinalIgnoreCase));
        }
    }
}