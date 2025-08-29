DECLARE @TableName SYSNAME = 'YourTableName';

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
WHERE c.TABLE_NAME = @TableName;