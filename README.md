public class GenericDbContext : DbContext
{
    public GenericDbContext(DbContextOptions<GenericDbContext> options) : base(options) { }

    public DbSet<MailDl> MailDls { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<MailDl>(entity =>
        {
            entity.HasKey(e => e.MailDlKey);
            entity.Property(e => e.MailDlKey).ValueGeneratedOnAdd();
            entity.Property(e => e.MailDlName).HasMaxLength(255).IsRequired();
            entity.Property(e => e.CreatedBy).HasMaxLength(100).IsRequired();
            entity.Property(e => e.CreatedAt).IsRequired();
        });
    }
}