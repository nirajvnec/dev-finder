if (!string.IsNullOrEmpty(report.ReportStatus))
{
    if (report.ReportStatus.Equals("Draft", StringComparison.OrdinalIgnoreCase) ||
        report.ReportStatus.Equals("Pending", StringComparison.OrdinalIgnoreCase))
    {
        report.SendForApproval = false;
    }
}