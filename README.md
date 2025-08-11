INSERT INTO [config].[app_setting] ([component_name], [environment], [setting_key], [setting_value], [is_active])
VALUES ('DataHubService', 'CRLocal', 'MarveLConfig:RollPrefix', 'MIM_ABT-GWG_', 1);


INSERT INTO [config].[app_setting] ([component_name], [environment], [setting_key], [setting_value], [is_active])
VALUES ('DataHubService', 'DEV', 'MarveLConfig:RollPrefix', 'MIM_ABT-GWG_', 1);


DELETE FROM [config].[app_setting]
WHERE [component_name] = 'DataHubService'
AND [environment] = 'CRLocal'
AND [setting_key] = 'MarveLConfig:RollPrefix'
AND [setting_value] = 'MIM_ABT-GWG_';


DELETE FROM [config].[app_setting]
WHERE [component_name] = 'DataHubService'
AND [environment] = 'DEV'
AND [setting_key] = 'MarveLConfig:RollPrefix'
AND [setting_value] = 'MIM_ABT-GWG_';