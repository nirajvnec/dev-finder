public static class ReflectionHelper
{
    public static async Task<object?> InvokeGenericAsync(
        object target,
        string methodName,
        object?[] parameters)
    {
        var method = target.GetType().GetMethod(methodName);
        if (method == null)
            throw new InvalidOperationException($"Method '{methodName}' not found on {target.GetType()}");

        var task = (Task)method.Invoke(target, parameters)!;
        await task.ConfigureAwait(false);

        // Handle Task<T> and Task
        var resultProperty = task.GetType().GetProperty("Result");
        return resultProperty != null ? resultProperty.GetValue(task) : null;
    }
}