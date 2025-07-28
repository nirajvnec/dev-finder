I wanted to mention that I haven’t had a chance to analyze these defects yet, as I believe they were previously assigned to Nancy. To be honest, I’m not fully aware of the background or current status at this point.

@Nancy – If you have any context or details regarding these bugs, could you please share them with us? That would be really helpful.








I haven’t had a chance to analyze these defects yet, as I believe they were previously assigned to Nancy. To be honest, I’m not fully aware of the background or current status at this point.





Hi Ram,

I've added the script for the QA environment as well — it's highlighted in yellow for easy reference.
Many thanks to Rajeev for his support.
Please go ahead with the next steps




Good that you reminded me—otherwise this would’ve ended up as a bug!





just tested it on my machine, and the uploaded_by column is coming as null for report ID 82. Looks like the development environment still has the old build. We may need to redeploy to Dev—doing that should sort out the issue.





Hi Rajeev,

Could you please share the Power BI workspace details in the following format:

[Name] – [Workspace Link]

For the below workspaces:

Treasury Risk

Credit Risk

Market Risk

Rates

Equities

FX





Hi Ram,

For the QA environment, I’m currently checking with Rajeev. If there are separate workspaces available for QA, I’ll update you accordingly.

For now, I’m sharing the details for Prod, Pre-Prod, and Dev environments.

Let me know if you need anything else.





Hi Rajeev,

As we now have the QA environment available, could you please share the corresponding Power BI workspace links for the following areas in QA?

Treasury Risk

Credit Risk

Market Risk

Rates

Equities


We already have the production links, so this would help us with QA validation and related activities.



Subject:

DDL & Insert Scripts for report_type and report_type_delivery_format Tables


---

✅ Email Body:

Hi Ram Gopal,

These are the scripts I'm sharing that are currently available — we have created these in the DB. Just sharing them here so we can get them checked in eventually.

Please get them reviewed with Pankaj first. If he finds everything is looking good, then you can proceed to check them into the SQLMI repository.

Also, make sure to get the release version from Lukas before checking in.

Let me know if anything needs to be updated.

Thanks,
Niraj


---




DELETE FROM [reference].[report_type_delivery_format];





DECLARE @pbi_key INT, @pg_key INT;

-- Get report_type_key for 'PBI' and 'PG'
SELECT @pbi_key = report_type_key
FROM [reference].[report_type]
WHERE report_type_name = 'PBI';

SELECT @pg_key = report_type_key
FROM [reference].[report_type]
WHERE report_type_name = 'PG';

-- Insert for PBI (PDF, PPTX)
INSERT INTO [reference].[report_type_delivery_format] (report_type_key, report_delivery_format_key)
SELECT @pbi_key, report_delivery_format_key
FROM [reference].[report_delivery_format]
WHERE report_delivery_format_name IN ('PDF', 'PPTX');

-- Insert for PG (all formats)
INSERT INTO [reference].[report_type_delivery_format] (report_type_key, report_delivery_format_key)
SELECT @pg_key, report_delivery_format_key
FROM [reference].[report_delivery_format]
WHERE report_delivery_format_name IN ('EXCEL', 'CSV', 'PDF', 'WORD', 'MHTML', 'PPTX');







INSERT INTO [reference].[report_type] (
    [report_type_name]
)
VALUES
('PBI'),
('PG');



INSERT INTO [reference].[role_prefix] (
    [role_prefix]
)
VALUES
('MIM_ABT-GWG_');




INSERT INTO [reference].[workspace_role_mapping] ([workspace_key], [role_id])
VALUES
(
    (SELECT workspace_key FROM [reference].[workspace] WHERE workspace_name = 'Treasury Risk'),
    (SELECT role_id FROM [reference].[role] WHERE role_name = 'PB-TR-VIEW')
),
(
    (SELECT workspace_key FROM [reference].[workspace] WHERE workspace_name = 'Credit'),
    (SELECT role_id FROM [reference].[role] WHERE role_name = 'PB-MTRM-CREDIT')
),
(
    (SELECT workspace_key FROM [reference].[workspace] WHERE workspace_name = 'Rates'),
    (SELECT role_id FROM [reference].[role] WHERE role_name = 'PB-MTRM-RATES')
),
(
    (SELECT workspace_key FROM [reference].[workspace] WHERE workspace_name = 'Market Risk'),
    (SELECT role_id FROM [reference].[role] WHERE role_name = 'PB-MR-VIEW')
),
(
    (SELECT workspace_key FROM [reference].[workspace] WHERE workspace_name = 'Equities'),
    (SELECT role_id FROM [reference].[role] WHERE role_name = 'PB-MTRM-EQUITIES')
),
(
    (SELECT workspace_key FROM [reference].[workspace] WHERE workspace_name = 'FX'),
    (SELECT role_id FROM [reference].[role] WHERE role_name = 'PB-MTRM-FX')
);








INSERT INTO [reference].[role] (
    [role_name],
    [description]
)
VALUES
('PB-TR-VIEW',       'Treasury Risk Viewer'),
('PB-MTRM-CREDIT',   'Credit Workspace Role'),
('PB-MTRM-RATES',    'Rates Workspace Role'),
('PB-MR-VIEW',       'Market Risk Viewer'),
('PB-MTRM-EQUITIES', 'Equities Workspace Role'),
('PB-MTRM-FX',       'FX Workspace Role');





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