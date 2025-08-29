public async Task<IEnumerable<TableEditorDataRow>> GetTableDataByTableNameAsync(string tableName)
{
    var result = new List<TableEditorDataRow>();

    // Extract schema + table
    var schema = "dbo"; 
    var pureTableName = tableName;

    if (tableName.Contains("."))
    {
        var parts = tableName.Split('.');
        schema = parts[0];
        pureTableName = parts[1];
    }

    // Validate table exists in schema
    var exists = await _context
        .Database
        .ExecuteSqlInterpolatedAsync(
            $"SELECT 1 FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA = {schema} AND TABLE_NAME = {pureTableName}");

    if (exists == 0)
        throw new ArgumentException($"Table '{schema}.{pureTableName}' does not exist.");

    // Run query
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