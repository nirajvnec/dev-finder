using Microsoft.EntityFrameworkCore;
using YourNamespace.Entities;

namespace YourNamespace.Data
{
    public class AppDbContext : DbContext
    {
        public DbSet<TableEditorMetadata> TableEditorMetadata { get; set; }
        public DbSet<TableEditorColumnMetadata> TableEditorColumnMetadata { get; set; }

        public AppDbContext(DbContextOptions<AppDbContext> options) : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            // 🟢 Parent entity
            modelBuilder.Entity<TableEditorMetadata>(entity =>
            {
                entity.ToTable("table_editor_metadata", "config");

                entity.HasKey(e => e.Id);

                entity.Property(e => e.TableName)
                      .HasMaxLength(200)
                      .IsRequired();
            });

            // 🟢 Child entity
            modelBuilder.Entity<TableEditorColumnMetadata>(entity =>
            {
                entity.ToTable("table_editor_column_metadata", "config");

                entity.HasKey(e => e.Id);

                entity.Property(e => e.ColumnName)
                      .HasMaxLength(200)
                      .IsRequired();

                // ⚡ Relationship mapping
                entity.HasOne(d => d.TableEditorMetadata)
                      .WithMany(p => p.Columns)
                      .HasForeignKey(d => d.TableEditorMetadataId)
                      .OnDelete(DeleteBehavior.Cascade);  // matches your SQL "ON DELETE CASCADE"
            });
        }
    }
}