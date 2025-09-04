public async Task<JsonElement?> GetByIdAsync(string entityName, string id)
{
    ValidateEntityName(entityName);
    ValidateId(entityName, id);

    try
    {
        var service = GetGenericService(entityName);
        var method = service.GetType().GetMethod("GetByIdAsync");
        
        // Convert string id to the correct key type
        var keyType = _entityTypeProvider.GetKeyType(entityName);
        object convertedId = ConvertId(id, keyType);
        
        var task = (Task)method.Invoke(service, new object[] { convertedId });
        await task;
        // ... rest of method
    }
}

private static object ConvertId(string id, Type keyType)
{
    if (keyType == typeof(int))
        return int.Parse(id);  // Convert "123" to 123
    if (keyType == typeof(string))
        return id;             // Keep as string
    
    throw new NotSupportedException($"Key type {keyType.Name} not supported");
}