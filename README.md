DECLARE @environment varchar(100) = $(Env);

IF UPPER(@environment) = 'DEV'
BEGIN
    IF NOT EXISTS (
        SELECT 1 
        FROM [config].[app_setting] 
        WHERE [component_name] = 'DataHubService' 
        AND [environment] = 'DEV' 
        AND [setting_key] = 'MarvelConfig:RolePrefix'
        AND [setting_value] = 'MIM_ABT-GWG_'
    )
    BEGIN
        INSERT INTO [config].[app_setting] ([component_name], [environment], [setting_key], [setting_value], [is_active])
        VALUES ('DataHubService', 'DEV', 'MarvelConfig:RolePrefix', 'MIM_ABT-GWG_', 1);
    END
END
ELSE IF UPPER(@environment) = 'TEST'
BEGIN
    IF NOT EXISTS (
        SELECT 1 
        FROM [config].[app_setting] 
        WHERE [component_name] = 'DataHubService' 
        AND [environment] = 'TEST' 
        AND [setting_key] = 'MarvelConfig:RolePrefix'
        AND [setting_value] = 'MIM_ABT-GWL_'
    )
    BEGIN
        INSERT INTO [config].[app_setting] ([component_name], [environment], [setting_key], [setting_value], [is_active])
        VALUES ('DataHubService', 'TEST', 'MarvelConfig:RolePrefix', 'MIM_ABT-GWL_', 1);
    END
END
ELSE IF UPPER(@environment) = 'PREPROD'
BEGIN
    IF NOT EXISTS (
        SELECT 1 
        FROM [config].[app_setting] 
        WHERE [component_name] = 'DataHubService' 
        AND [environment] = 'PREPROD' 
        AND [setting_key] = 'MarvelConfig:RolePrefix'
        AND [setting_value] = 'MIM_ABT-GWN_'
    )
    BEGIN
        INSERT INTO [config].[app_setting] ([component_name], [environment], [setting_key], [setting_value], [is_active])
        VALUES ('DataHubService', 'PREPROD', 'MarvelConfig:RolePrefix', 'MIM_ABT-GWN_', 1);
    END
END
ELSE IF UPPER(@environment) = 'PROD'
BEGIN
    IF NOT EXISTS (
        SELECT 1 
        FROM [config].[app_setting] 
        WHERE [component_name] = 'DataHubService' 
        AND [environment] = 'PROD' 
        AND [setting_key] = 'MarvelConfig:RolePrefix'
        AND [setting_value] = 'MIM_ABT-GWS_'
    )
    BEGIN
        INSERT INTO [config].[app_setting] ([component_name], [environment], [setting_key], [setting_value], [is_active])
        VALUES ('DataHubService', 'PROD', 'MarvelConfig:RolePrefix', 'MIM_ABT-GWS_', 1);
    END
END