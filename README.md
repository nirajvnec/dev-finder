DELETE FROM [reference].[workspace]
WHERE [workspace_key] > 12;



INSERT INTO [reference].[workspace] (
    [workspace_name],
    [workspace_guid],
    [workspace_url],
    [is_active],
    [created_at],
    [created_by]
)
VALUES
('Treasury Risk', 'GUID_PLACEHOLDER_1', 'URL_PLACEHOLDER_1', 1, GETDATE(), 'system'),
('Credit',        'GUID_PLACEHOLDER_2', 'URL_PLACEHOLDER_2', 1, GETDATE(), 'system'),
('Rates',         'GUID_PLACEHOLDER_3', 'URL_PLACEHOLDER_3', 1, GETDATE(), 'system'),
('Market Risk',   'GUID_PLACEHOLDER_4', 'URL_PLACEHOLDER_4', 1, GETDATE(), 'system'),
('Equities',      'GUID_PLACEHOLDER_5', 'URL_PLACEHOLDER_5', 1, GETDATE(), 'system'),
('FX',            'GUID_PLACEHOLDER_6', 'URL_PLACEHOLDER_6', 1, GETDATE(), 'system');







Hi Raja Gopal,

I hope you're doing well. With the new test environment set up, I believe we don't directly handle the deployment—Melia will take care of it. But do we need to provide her with a build or some kind of release link for NILIA? Also, could you please clarify how the API and DB scripts will be handled in this context?