using Microsoft.EntityFrameworkCore;
using System.Threading.Tasks;
using System.Data.Common;

public static class DbSchemaExtensions
{
    /// <summary>
    /// Checks whether a given schema.table exists in the database.
    /// </summary>
    public static async Task<bool> TableExistsAsync(this DbContext context, string schema, string tableName)
    {
        await using var connection = context.Database.GetDbConnection();
        await connection.OpenAsync();

        await using var command = connection.CreateCommand();
        command.CommandText = @"
            SELECT COUNT(*) 
            FROM INFORMATION_SCHEMA.TABLES 
            WHERE TABLE_SCHEMA = @schema AND TABLE_NAME = @table";
        
        var schemaParam = command.CreateParameter();
        schemaParam.ParameterName = "@schema";
        schemaParam.Value = schema;
        command.Parameters.Add(schemaParam);

        var tableParam = command.CreateParameter();
        tableParam.ParameterName = "@table";
        tableParam.Value = tableName;
        command.Parameters.Add(tableParam);

        var result = (int)(await command.ExecuteScalarAsync());
        return result > 0;
    }
}