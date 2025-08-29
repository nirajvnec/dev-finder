public class ColumnSchema
{
    public string ColumnName { get; set; } = string.Empty;
    public string DataType { get; set; } = string.Empty;
    public int? CharacterMaximumLength { get; set; }
    public int? NumericPrecision { get; set; }
    public bool IsRequired { get; set; }
    public bool IsReadOnly { get; set; }
}