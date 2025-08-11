var builder = WebApplication.CreateBuilder(args);

builder.Configuration.AddMarvelFileConfiguration();
builder.Configuration.AddMarvelDbConfiguration();

builder.Services.Configure<RoleConfig>(options => builder.Configuration.GetSection(key: "MarvelConfig").Bind(options));
builder.Services.Configure<MarvelAppConfig>(options => builder.Configuration.GetSection(key: "MarvelAppConfig").Bind(options));
builder.Services.Configure<RdlReportConfig>(options => builder.Configuration.GetSection(key: "MarvelRdlReportConfig").Bind(options));

builder.Services.AddApplicationInsightsTelemetry();
builder.Services.AddMemoryCache();

builder.Host.UseSerilog((hostingContext, services, loggerConfiguration) =>
    loggerConfiguration
        .ReadFrom.Configuration(hostingContext.Configuration)
        .WriteTo.ApplicationInsights(
            telemetryConfiguration: services.GetRequiredService<TelemetryConfiguration>(),
            TelemetryConverter.Traces
        )
);

builder.WebHost.UseKestrel();

// Build the application
var app = builder.Build();

// Resolve and check RoleConfig
using (var scope = app.Services.CreateScope())
{
    var roleConfig = scope.ServiceProvider.GetRequiredService<IOptions<RoleConfig>>().Value;
    Console.WriteLine($"RolePrefix is populated: {roleConfig.RolePrefix ?? "Not populated"}");
}

app.Run();



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