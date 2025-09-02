using System;
using System.Reflection;

public static class ReflectionHelper
{
    public static Type? GetEntityType(string entityName)
    {
        // Load the assembly where your entities are defined
        var assembly = Assembly.Load("Marvel.OperationalServices.Repositories");

        // Adjust namespace if needed
        var fullName = $"Marvel.OperationalServices.Repositories.Entities.{entityName}";

        return assembly.GetType(fullName);
    }
}