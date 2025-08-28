protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<TableEditorColumnMetadata>()
        .HasOne(c => c.TableEditorMetadata)
        .WithMany(p => p.Columns)
        .HasForeignKey(c => c.TableEditorMetadataId)
        .OnDelete(DeleteBehavior.Cascade);

    // Always call base!
    base.OnModelCreating(modelBuilder);
}