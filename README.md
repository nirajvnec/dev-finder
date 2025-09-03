// Add generic repository and service registrations
builder.Services.AddScoped<IGenericRepository<MailDl, int>, GenericRepository<MailDl, int>>();
builder.Services.AddScoped<IGenericService<MailDl, int>, GenericService<MailDl, int>>();

// Add EntityServiceFactory and EntityTypeProvider
builder.Services.AddScoped<IEntityServiceFactory, EntityServiceFactory>();
builder.Services.AddSingleton<IEntityTypeProvider, EntityTypeProvider>();