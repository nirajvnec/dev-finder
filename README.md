using System;
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace YourNamespace
{
    [Table("table_editor_metadata", Schema = "config")]
    public class TableEditorMetadata
    {
        [Key]
        [Column("id")]
        public int Id { get; set; }

        [Column("table_name")]
        [Required]
        [MaxLength(300)]
        public string TableName { get; set; }

        [Column("display_name")]
        [MaxLength(200)]
        public string? DisplayName { get; set; }

        [Column("is_active")]
        [Required]
        public bool IsActive { get; set; }

        [Column("created_at")]
        [Required]
        public DateTime CreatedAt { get; set; }

        [Column("updated_at")]
        public DateTime? UpdatedAt { get; set; }
    }
}