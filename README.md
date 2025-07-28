using Marvel.Reference.ServiceContract.Contracts;
using Microsoft.AspNetCore.Mvc;

namespace Marvel.Reference.ApiHost.Controllers
{
    [ApiController]
    [Route("reference/[controller]")]
    public class ReportMetadataController : ControllerBase
    {
        private readonly ILogger<ReportMetadataController> _logger;
        private readonly IReportMetaDataService _reportMetaDataService;

        public ReportMetadataController(
            ILogger<ReportMetadataController> logger,
            IReportMetaDataService reportMetaDataService)
        {
            _logger = logger;
            _reportMetaDataService = reportMetaDataService;
        }

        /// <summary>
        /// Get all report file locations for all report types
        /// </summary>
        [HttpGet("file-locations")]
        public async Task<ActionResult<IEnumerable<ReportFileLocationResponse>>> GetReportFileLocationsAsync()
        {
            var result = await _reportMetaDataService.GetReportFileLocationsAsync();
            return Ok(result);
        }
    }
}








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