public async Task<IEnumerable<ReportFileLocationResponse>> GetReportFileLocationsAsync()
{
    var items = await _context.ReportSharedFolders
        .Where(x => x.ReportSharedFolderName != null)
        .ToListAsync();

    var grouped = items
        .GroupBy(x => x.ReportSharedReportType)
        .Select(g => new ReportFileLocationResponse
        {
            ReportType = g.Key,
            Locations = g.Select(x => new ReportFileLocationItem
            {
                DisplayText = x.ReportSharedFolderName,
                DisplayValue = x.ReportSharedFolderName
            }).ToList()
        })
        .ToList();

    return grouped;
}