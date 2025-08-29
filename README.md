public class ColumnSchema
{
    public string COLUMN_NAME { get; set; }
    public string DATA_TYPE { get; set; }
    public int? CHARACTER_MAXIMUM_LENGTH { get; set; }
    public int? NUMERIC_PRECISION { get; set; }
}







public class YourDbContext : DbContext
{
    public YourDbContext(DbContextOptions<YourDbContext> options)
        : base(options)
    {
    }

    public DbSet<TableEditorMetadata> TableEditorMetadata { get; set; }
    public DbSet<TableEditorColumnMetadata> TableEditorColumnMetadata { get; set; }

    // Add this line:
    public DbSet<ColumnSchema> ColumnSchemas { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        // Mark ColumnSchema as a keyless entity because it maps to INFORMATION_SCHEMA
        modelBuilder.Entity<ColumnSchema>().HasNoKey();
    }
}