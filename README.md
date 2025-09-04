, MailDlValidator>();

// Register specific service (not generic service)
builder.Services.AddScoped<IMailDlService, MailDlService>();

// Still need repository
builder.Services.AddScoped<IGenericRepository<MailDl, int>, GenericRepository<MailDl, int>>();
*