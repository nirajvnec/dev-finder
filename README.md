public interface IReportMetadataService
{
    Task<IEnumerable<ReportFileLocationResponse>> GetReportFileLocationsAsync();
}






public class ReportFileLocationItem
{
    public string DisplayText { get; set; } = string.Empty;
    public string DisplayValue { get; set; } = string.Empty;
}



public class ReportFileLocationResponse
{
    public string ReportType { get; set; } = string.Empty;
    public List<ReportFileLocationItem> Locations { get; set; } = new();
}