IF NOT EXISTS (
    SELECT * 
    FROM [Reference].[role]
    WHERE [role_name] = 'PB-TR-VIEW'
      AND CAST([description] AS VARCHAR(MAX)) = 'Treasury Risk Viewer'
)
BEGIN
    INSERT INTO [Reference].[role] ([role_name], [description])
    VALUES ('PB-TR-VIEW', 'Treasury Risk Viewer')
END

IF NOT EXISTS (
    SELECT * 
    FROM [Reference].[role]
    WHERE [role_name] = 'PB-MTRM-CREDIT'
      AND CAST([description] AS VARCHAR(MAX)) = 'Credit Workspace Role'
)
BEGIN
    INSERT INTO [Reference].[role] ([role_name], [description])
    VALUES ('PB-MTRM-CREDIT', 'Credit Workspace Role')
END

IF NOT EXISTS (
    SELECT * 
    FROM [Reference].[role]
    WHERE [role_name] = 'PB-MTRM-RATES'
      AND CAST([description] AS VARCHAR(MAX)) = 'Rates Workspace Role'
)
BEGIN
    INSERT INTO [Reference].[role] ([role_name], [description])
    VALUES ('PB-MTRM-RATES', 'Rates Workspace Role')
END

IF NOT EXISTS (
    SELECT * 
    FROM [Reference].[role]
    WHERE [role_name] = 'PB-MTRM-EQUITIES'
      AND CAST([description] AS VARCHAR(MAX)) = 'Equities Workspace Role'
)
BEGIN
    INSERT INTO [Reference].[role] ([role_name], [description])
    VALUES ('PB-MTRM-EQUITIES', 'Equities Workspace Role')
END

IF NOT EXISTS (
    SELECT * 
    FROM [Reference].[role]
    WHERE [role_name] = 'PB-MTRM-FX'
      AND CAST([description] AS VARCHAR(MAX)) = 'FX Workspace Role'
)
BEGIN
    INSERT INTO [Reference].[role] ([role_name], [description])
    VALUES ('PB-MTRM-FX', 'FX Workspace Role')
END