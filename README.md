using Microsoft.EntityFrameworkCore;
using System.Threading.Tasks;

public static class DbSchemaExtensions
{
    /// <summary>
    /// Splits "schema.table" into (schema, table). Defaults schema to "dbo" if missing.
    /// </summary>
    public static (string Schema, string Table) SplitSchemaAndTable(this string tableName)
    {
        if (string.IsNullOrWhiteSpace(tableName))
            throw new ArgumentException("Table name cannot be null or empty.", nameof(tableName));

        if (tableName.Contains("."))
        {
            var parts = tableName.Split('.', 2); // only split once
            return (parts[0], parts[1]);
        }

        return ("dbo", tableName);
    }

    /// <summary>
    /// Checks whether a given schema.table exists in the database.
    /// </summary>
    public static async Task<bool> TableExistsAsync(this DbContext context, string schema, string tableName)
    {
        var count = await context.Database
            .ExecuteSqlInterpolatedAsync(
                $"SELECT COUNT(*) FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA = {schema} AND TABLE_NAME = {tableName}");

        return count > 0;
    }
}





using Microsoft.EntityFrameworkCore;
using System.Threading.Tasks;

public static class DbSchemaExtensions
{
    /// <summary>
    /// Checks whether a given schema.table exists in the database.
    /// </summary>
    public static async Task<bool> TableExistsAsync(this DbContext context, string schema, string tableName)
    {
        var sql = @"
            SELECT COUNT(*) 
            FROM INFORMATION_SCHEMA.TABLES 
            WHERE TABLE_SCHEMA = {0} AND TABLE_NAME = {1}";

        var count = await context.Database
            .ExecuteSqlInterpolatedAsync(
                $"SELECT COUNT(*) FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA = {schema} AND TABLE_NAME = {tableName}");

        return count > 0;
    }
}




public async Task<IEnumerable<TableEditorDataRow>> GetTableDataByTableNameAsync(string tableName)
{
    var result = new List<TableEditorDataRow>();

    // Split schema + table
    var schema = "dbo";
    var pureTableName = tableName;
    if (tableName.Contains("."))
    {
        var parts = tableName.Split('.');
        schema = parts[0];
        pureTableName = parts[1];
    }

    // Use extension method
    if (!await _context.TableExistsAsync(schema, pureTableName))
        throw new ArgumentException($"Table '{schema}.{pureTableName}' does not exist.");

    using (var connection = _context.Database.GetDbConnection())
    {
        await connection.OpenAsync();

        using (var command = connection.CreateCommand())
        {
            command.CommandText = $"SELECT * FROM [{schema}].[{pureTableName}]";

            using (var reader = await command.ExecuteReaderAsync())
            {
                while (await reader.ReadAsync())
                {
                    var row = new TableEditorDataRow();

                    for (int i = 0; i < reader.FieldCount; i++)
                    {
                        string columnName = reader.GetName(i);
                        object value = await reader.IsDBNullAsync(i) ? null : reader.GetValue(i);
                        row.Data[columnName] = value;
                    }

                    result.Add(row);
                }
            }
        }
    }

    return result;
}
                    result.Add(row);
                }
            }
        }
    }

    return result;
}