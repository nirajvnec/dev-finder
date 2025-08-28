using System;
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace Elections
{
    [Table("table_editor_metadata", Schema = "config")]
    public class TableEditorMetadata
    {
        [Key]
        [Column("id")]
        public int Id { get; set; }

        [Column("table_name")]
        public string TableName { get; set; } = string.Empty;

        [Column("created_at")]
        public DateTime CreatedAt { get; set; }

        [Column("updated_at")]
        public DateTime? UpdatedAt { get; set; }

        // 🔗 Navigation property (one-to-many)
        public ICollection<TableEditorColumnMetadata> Columns { get; set; }
            = new List<TableEditorColumnMetadata>();
    }

    [Table("table_editor_column_metadata", Schema = "config")]
    public class TableEditorColumnMetadata
    {
        [Key]
        [Column("id")]
        public int Id { get; set; }

        // FK column (must match SQL column)
        [Column("table_metadata_id")]
        public int TableEditorMetadataId { get; set; }

        [Column("column_name")]
        public string ColumnName { get; set; } = string.Empty;

        [Column("is_hidden_in_ui")]
        public bool IsHiddenInUi { get; set; }

        [Column("is_hidden_in_edit_model")]
        public bool IsHiddenInEditModel { get; set; }

        [Column("created_at")]
        public DateTime CreatedAt { get; set; }

        [Column("updated_at")]
        public DateTime? UpdatedAt { get; set; }

        // 🔗 Navigation (many-to-one)
        [ForeignKey(nameof(TableEditorMetadataId))]
        public TableEditorMetadata? TableEditorMetadata { get; set; }
    }
}