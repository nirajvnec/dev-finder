public static class EntityHelper
{
    public static string GetIdFromEntity(JsonElement entity)
    {
        if (entity.TryGetProperty("mailDlKey", out JsonElement mailDlKeyElement))
            return mailDlKeyElement.ToString();
        if (entity.TryGetProperty("id", out JsonElement idElement))
            return idElement.ToString();
        if (entity.TryGetProperty("Id", out JsonElement upperIdElement))
            return upperIdElement.ToString();
        
        return string.Empty;
    }
}