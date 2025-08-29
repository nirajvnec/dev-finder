param(
    [string]$ConnectionString = "Server=YOUR_SERVER;Database=YOUR_DB;User Id=YOUR_USER;Password=YOUR_PASSWORD;",
    [string]$Provider = "Microsoft.EntityFrameworkCore.SqlServer"
)

# Go to the directory where your .csproj exists
# (adjust this if your project is not in the same folder as this script)
Set-Location -Path (Split-Path $MyInvocation.MyCommand.Path)

# Run scaffold
dotnet ef dbcontext scaffold $ConnectionString $Provider `
  -c FeatureDbContext `
  -o Models/Feature `
  --data-annotations `
  -t reference.mail_dl_key `
  -t reference.mail_dl_metadata `
  -t reference.user_profile `
  -t reference.user_role `
  -t reference.audit_log `
  -t reference.report_config `
  -t reference.report_file_location `
  -t reference.role_permission `
  -t reference.department `
  -t reference.project