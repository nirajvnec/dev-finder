IF NOT EXISTS (
    SELECT 1
    FROM sys.tables t
    JOIN sys.schemas s ON t.schema_id = s.schema_id
    WHERE t.name = 'table_editor_column_metadata'
      AND s.name = 'config'
)
BEGIN
    CREATE TABLE [config].[table_editor_column_metadata](
        [id] INT IDENTITY(1,1) NOT NULL PRIMARY KEY,
        [table_metadata_id] INT NOT NULL,   -- FK to table_editor_metadata
        [column_name] NVARCHAR(200) NOT NULL,
        [is_hidden_column] BIT NOT NULL DEFAULT (0),
        [is_hidden_in_edit_mode] BIT NOT NULL DEFAULT (0),
        [created_at] DATETIME2(3) NOT NULL DEFAULT (sysutcdatetime()),
        [updated_at] DATETIME2(3) NULL,

        CONSTRAINT FK_table_editor_column_metadata_table
            FOREIGN KEY (table_metadata_id) 
            REFERENCES [config].[table_editor_metadata](id)
            ON DELETE CASCADE
    );
END
GO