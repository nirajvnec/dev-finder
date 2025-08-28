using System;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

[Table("table_editor_column_metadata", Schema = "config")]
public class TableEditorColumnMetadata
{
    [Key]
    [Column("id")]
    public int Id { get; set; }

    [Required]
    [Column("table_id")]
    public int TableId { get; set; }

    [Required]
    [MaxLength(200)]
    [Column("column_name")]
    public string ColumnName { get; set; }

    [MaxLength(200)]
    [Column("display_name")]
    public string DisplayName { get; set; }

    [Required]
    [Column("is_active")]
    public bool IsActive { get; set; }

    [Column("created_at")]
    public DateTime CreatedAt { get; set; }

    [Column("updated_at")]
    public DateTime UpdatedAt { get; set; }

    // 🔗 Navigation property - each column belongs to one table
    [ForeignKey("TableId")]
    public TableEditorMetadata Table { get; set; }
}



using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

[Table("table_editor_metadata", Schema = "config")]
public class TableEditorMetadata
{
    [Key]
    [Column("id")]
    public int Id { get; set; }

    [Required]
    [MaxLength(300)]
    [Column("table_name")]
    public string TableName { get; set; }

    [MaxLength(200)]
    [Column("display_name")]
    public string DisplayName { get; set; }

    [Required]
    [Column("is_active")]
    public bool IsActive { get; set; }

    [Column("created_at")]
    public DateTime CreatedAt { get; set; }

    [Column("updated_at")]
    public DateTime UpdatedAt { get; set; }

    // 🔗 Navigation property - One table has many columns
    public ICollection<TableEditorColumnMetadata> Columns { get; set; }
}