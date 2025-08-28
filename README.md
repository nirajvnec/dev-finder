using System.Collections.Generic;

namespace YourNamespace.Models
{
    public class TableEditorDataRow
    {
        public Dictionary<string, object?> Data { get; set; } = new();
    }
}



namespace YourNamespace.Models
{
    public class TableEditorColumnsInfo
    {
        public string Name { get; set; } = string.Empty;
        public string DisplayName { get; set; } = string.Empty;
        public string Type { get; set; } = string.Empty;
        public int Length { get; set; }
        public int? NumberDecimal { get; set; } // optional
        public bool? IsRequired { get; set; }       // optional
        public bool? IsReadOnly { get; set; }       // optional
        public bool? HideInEditMode { get; set; }   // optional
        public bool? IsHiddenColumn { get; set; }   // optional
    }
}



namespace YourNamespace.Models
{
    public class TableEditorTableNames
    {
        public string Name { get; set; } = string.Empty;
        public string? DisplayName { get; set; } // optional
    }
}