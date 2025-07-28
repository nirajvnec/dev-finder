public DbSet<ReportSharedFolder> ReportSharedFolders { get; set; }




using System;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace MarvelReference.Repositories.Entities
{
    [Table("report_shared_folder", Schema = "reference")]
    public class ReportSharedFolder
    {
        [Key]
        [Column("report_shared_folder_key")]
        public int ReportSharedFolderKey { get; set; }

        [Column("report_shared_folder_name")]
        [MaxLength(1000)]
        public string ReportSharedFolderName { get; set; } = string.Empty;

        [Column("report_shared_report_type")]
        [MaxLength(1000)]
        public string ReportSharedReportType { get; set; } = string.Empty;

        [Column("created_dt")]
        public DateTime? CreatedDt { get; set; }

        [Column("created_by")]
        [MaxLength(100)]
        public string? CreatedBy { get; set; }
    }
}












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