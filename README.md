dotnet ef dbcontext scaffold "YourConnectionString" Microsoft.EntityFrameworkCore.SqlServer `
  -c FeatureDbContext `
  -o Models/Feature `
  --data-annotations `
  -t reference.mail_dl_key `
  ...