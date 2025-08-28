DROP TABLE [config].[table_editor_column_metadata];
GO

CREATE TABLE [config].[table_editor_column_metadata] (
    [id] INT IDENTITY(1,1) NOT NULL PRIMARY KEY,
    [table_editor_metadata_id] INT NOT NULL,   -- ✅ corrected column name
    [column_name] NVARCHAR(200) NOT NULL,
    [is_hidden_column] BIT NOT NULL DEFAULT (0),
    [is_added_in_edit_model] BIT NOT NULL DEFAULT (0),
    [created_at] DATETIME2(3) NOT NULL DEFAULT (SYSUTCDATETIME()),
    [updated_at] DATETIME2(3) NULL DEFAULT (SYSUTCDATETIME()),

    CONSTRAINT FK_table_editor_column_metadata_table
        FOREIGN KEY ([table_editor_metadata_id])
        REFERENCES [config].[table_editor_metadata]([id])
        ON DELETE CASCADE
);
GO