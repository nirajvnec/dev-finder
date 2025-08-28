-- Create table if it does not exist
IF NOT EXISTS (
    SELECT 1
    FROM sys.tables t
    INNER JOIN sys.schemas s ON t.schema_id = s.schema_id
    WHERE t.name = 'table_editor_metadata'
      AND s.name = 'config'
)
BEGIN
    CREATE TABLE config.table_editor_metadata
    (
        id INT IDENTITY(1,1) PRIMARY KEY,
        table_name NVARCHAR(300) NOT NULL,   -- schema-qualified name (e.g., "reference.mail_dl")
        display_name NVARCHAR(200) NULL,     -- friendly name for UI
        is_active BIT NOT NULL DEFAULT 1,    -- visibility flag
        created_at DATETIME2(3) NOT NULL DEFAULT SYSUTCDATETIME(),
        updated_at DATETIME2(3) NULL
    );
END
GO

-- Insert seed record for reference.mail_dl if it doesn't exist
IF NOT EXISTS (
    SELECT 1 
    FROM config.table_editor_metadata
    WHERE table_name = 'reference.mail_dl'
)
BEGIN
    INSERT INTO config.table_editor_metadata (table_name, display_name)
    VALUES ('reference.mail_dl', 'Mail DL Table');
END
GO