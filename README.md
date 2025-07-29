INSERT INTO [reference].[report_report_shared_folder] (
    [report_report_shared_folder_name],
    [report_report_shared_report_type],
    [created_at],
    [created_by]
)
VALUES
    ('mr-marvel-report-export', 'PBI', GETDATE(), 'system'),
    ('mr-marvel-report-export', 'PPT', GETDATE(), 'system');




CREATE TABLE [reference].[mail_dl] (
    [mail_dl_key] INT IDENTITY(1,1) NOT NULL,
    [mail_dl_name] NVARCHAR(255) NOT NULL,
    [created_at] DATETIME NOT NULL DEFAULT GETDATE(),
    [created_by] NVARCHAR(100) NOT NULL
);













="INSERT INTO [reference].[mail_dl] ([mail_dl_name], [created_at], [created_by]) VALUES ('" & E2 & "', GETDATE(), 'system');"







="INSERT INTO [reference].[mail_dl] ([worker], [created_at], [created_by]) VALUES ('" & E2 & "', GETDATE(), 'system');"



CREATE TABLE [reference].[mail_dl] (
    [id] INT IDENTITY(1,1) NOT NULL,
    [worker] VARCHAR(300) NULL,
    [created_at] DATETIME NOT NULL DEFAULT GETDATE(),
    [created_by] VARCHAR(100) NOT NULL DEFAULT 'system'
);






INSERT INTO [reference].[report_shared_folder] (
    [report_report_shared_folder_name],
    [report_report_shared_report_type],
    [created_at],
    [created_by]
)
VALUES
    ('mr-marvel-report-export', 'pbi', GETDATE(), 'system'),
    ('mr-marvel-report-export', 'pg', GETDATE(), 'system');





UPDATE [reference].[report_shared_folder]
SET 
    [created_at] = GETDATE(),
    [created_by] = 'system'
WHERE 
    [report_report_shared_folder_name] = 'mr-marvel-report-export'
    AND [created_at] IS NULL
    AND [created_by] IS NULL;



INSERT INTO [reference].[report_shared_folder] (
    [report_report_shared_folder_name],
    [report_report_shared_report_type]
)
VALUES
    ('mr-marvel-report-export', 'pbi'),
    ('mr-marvel-report-export', 'pg');