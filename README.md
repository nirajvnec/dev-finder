using System;
using System.Linq;
using System.Reflection;

namespace Marvel.OperationalServices.Common.Helpers
{
    public static class ReflectionHelper
    {
        /// <summary>
        /// Gets the Type of an entity by its name using reflection.
        /// </summary>
        /// <param name="entityName">Name of the entity (case-insensitive, without namespace).</param>
        /// <param name="assembly">Optional assembly where entities are defined. If null, searches all loaded assemblies.</param>
        /// <returns>Entity Type if found, otherwise throws exception.</returns>
        public static Type GetEntityType(string entityName, Assembly? assembly = null)
        {
            if (string.IsNullOrWhiteSpace(entityName))
                throw new ArgumentException("Entity name cannot be null or empty", nameof(entityName));

            // Pick specific assembly if provided, else search in all
            var assembliesToSearch = assembly != null
                ? new[] { assembly }
                : AppDomain.CurrentDomain.GetAssemblies();

            // Try to find matching type
            var entityType = assembliesToSearch
                .SelectMany(a => a.GetTypes())
                .FirstOrDefault(t => t.Name.Equals(entityName, StringComparison.OrdinalIgnoreCase));

            if (entityType == null)
                throw new InvalidOperationException($"Entity type '{entityName}' was not found in loaded assemblies.");

            return entityType;
        }
    }
}