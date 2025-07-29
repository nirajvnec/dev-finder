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