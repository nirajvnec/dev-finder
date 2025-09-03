public class GenericDbContext : DbContext  // Make sure this is correct
{
    public GenericDbContext(DbContextOptions<GenericDbContext> options) : base(options) { }
    
    public DbSet<MailDl> MailDls { get; set; }
}