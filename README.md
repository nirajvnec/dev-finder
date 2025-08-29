public async Task<IEnumerable<TableEditorColumnsInfo>> GetTableSchemaByTableNameAsync(string tableName)
{
    var result = new List<TableEditorColumnsInfo>();

    // 1. Get the table metadata entry (from table_editor_metadata)
    var tableMeta = await _context.TableEditorMetadata
        .FirstOrDefaultAsync(t => t.TableName == tableName);

    if (tableMeta == null)
        throw new KeyNotFoundException($"Table '{tableName}' not found in TableEditorMetadata.");

    // 2. Get column metadata (from table_editor_column_metadata)
    var columnMetadata = await _context.TableEditorColumnMetadata
        .Where(c => c.TableEditorMetadataId == tableMeta.Id)
        .ToListAsync();

    // 3. Get actual schema info from INFORMATION_SCHEMA
    var columns = await _context.ColumnSchemas
        .FromSqlRaw(@"
            SELECT COLUMN_NAME, DATA_TYPE, CHARACTER_MAXIMUM_LENGTH, NUMERIC_PRECISION
            FROM INFORMATION_SCHEMA.COLUMNS
            WHERE TABLE_NAME = {0}", tableName)
        .ToListAsync();

    // 4. Merge schema info with column metadata
    foreach (var col in columns)
    {
        var meta = columnMetadata.FirstOrDefault(m => m.ColumnName == col.COLUMN_NAME);

        result.Add(new TableEditorColumnsInfo
        {
            Name = col.COLUMN_NAME,
            DisplayName = meta?.DisplayName ?? col.COLUMN_NAME,
            Type = col.DATA_TYPE,
            Length = col.CHARACTER_MAXIMUM_LENGTH ?? 0,
            NumberDecimal = col.NUMERIC_PRECISION ?? 0,
            IsRequired = meta?.IsRequired ?? false,
            IsReadOnly = meta?.IsReadOnly ?? false,
            HideInEditMode = meta?.HideInEditMode ?? false,
            IsHiddenColumn = meta?.IsHiddenColumn ?? false
        });
    }

    return result;
}