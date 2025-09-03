if (!string.IsNullOrEmpty(report.ReportStatus) &&
    (report.ReportStatus == "Draft" || report.ReportStatus == "Pending"))
{
    report.SendForApproval = false;
}