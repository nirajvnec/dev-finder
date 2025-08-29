public async Task<IEnumerable<TableEditorDataRow>> GetTableDataByTableNameAsync(string tableName)
{
    var result = new List<TableEditorDataRow>();

    // Parse schema + table
    var (schema, pureTableName) = tableName.SplitSchemaAndTable();

    // Validate existence
    if (!await _context.TableExistsAsync(schema, pureTableName))
        throw new ArgumentException($"Table '{schema}.{pureTableName}' does not exist.");

    // Query rows
    using var connection = _context.Database.GetDbConnection();
    await connection.OpenAsync();

    using var command = connection.CreateCommand();
    command.CommandText = $"SELECT * FROM [{schema}].[{pureTableName}]";

    using var reader = await command.ExecuteReaderAsync();
    while (await reader.ReadAsync())
    {
        var row = new TableEditorDataRow();

        for (int i = 0; i < reader.FieldCount; i++)
        {
            string columnName = reader.GetName(i);
            object? value = await reader.IsDBNullAsync(i) ? null : reader.GetValue(i);
            row.Data[columnName] = value;
        }

        result.Add(row);
    }

    return result;
}