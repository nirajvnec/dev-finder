public async Task<IEnumerable<TableEditorColumnInfo>> GetTableSchemaByTableNameAsync(string tableName)
{
    if (string.IsNullOrWhiteSpace(tableName))
        throw new ArgumentException("Table name cannot be null or empty.", nameof(tableName));

    // 1. Parse schema and table name
    var parts = tableName.Split('.', 2, StringSplitOptions.RemoveEmptyEntries);
    var schemaName = parts.Length == 2 ? parts[0] : "dbo";   // default schema is dbo
    var pureTableName = parts.Length == 2 ? parts[1] : parts[0];

    // 2. Check if table exists in TableEditorMetadata (without schema)
    var tableMeta = await _context.TableEditorMetadata
        .FirstOrDefaultAsync(t => t.TableName == pureTableName);

    if (tableMeta == null)
        throw new KeyNotFoundException($"Table '{pureTableName}' not found in TableEditorMetadata.");

    // 3. Load column metadata overrides (if any)
    var columnMetadata = await _context.TableEditorColumnMetadata
        .Where(c => c.TableEditorMetadataId == tableMeta.Id)
        .ToListAsync();

    // 4. Get actual schema info from INFORMATION_SCHEMA + sys tables
    var schemaColumns = await _context.ColumnSchemas
        .FromSqlRaw(@"
            SELECT 
                c.COLUMN_NAME AS ColumnName,
                c.DATA_TYPE AS DataType,
                c.CHARACTER_MAXIMUM_LENGTH AS CharacterMaximumLength,
                c.NUMERIC_PRECISION AS NumericPrecision,
                CASE WHEN c.IS_NULLABLE = 'NO' THEN CAST(1 AS bit) ELSE CAST(0 AS bit) END AS IsRequired,
                CASE 
                    WHEN col.is_identity = 1 OR pk.column_id IS NOT NULL THEN CAST(1 AS bit) 
                    ELSE CAST(0 AS bit) 
                END AS IsReadOnly
            FROM INFORMATION_SCHEMA.COLUMNS c
            JOIN sys.columns col 
                ON col.object_id = OBJECT_ID(c.TABLE_SCHEMA + '.' + c.TABLE_NAME)
                AND col.name = c.COLUMN_NAME
            LEFT JOIN sys.indexes i 
                ON i.object_id = col.object_id 
                AND i.is_primary_key = 1
            LEFT JOIN sys.index_columns pk
                ON pk.object_id = col.object_id 
                AND pk.column_id = col.column_id 
                AND pk.index_id = i.index_id
            WHERE c.TABLE_SCHEMA = {0} AND c.TABLE_NAME = {1};
        ", schemaName, pureTableName)
        .ToListAsync();

    // 5. Merge schema info with metadata (apply overrides where available)
    var result = schemaColumns.Select(sc =>
    {
        var meta = columnMetadata.FirstOrDefault(m => m.ColumnName == sc.ColumnName);

        return new TableEditorColumnInfo
        {
            ColumnName = sc.ColumnName,
            DataType = sc.DataType,
            MaxLength = sc.CharacterMaximumLength,
            NumericPrecision = sc.NumericPrecision,
            IsRequired = sc.IsRequired,
            IsReadOnly = sc.IsReadOnly,
            HideInEditMode = meta?.HideInEditMode ?? false,
            IsHidden = meta?.IsHidden ?? false,
            DisplayName = meta?.DisplayName ?? sc.ColumnName
        };
    }).ToList();

    return result;
}