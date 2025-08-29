using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;

namespace Marvel.OperationalServices.Infrastructure
{
    public interface IMarvelDbContextFactory
    {
        MarvelDbContext CreateDbContext();
    }

    public class MarvelDbContextFactory : IMarvelDbContextFactory
    {
        private readonly IConfiguration _configuration;

        public MarvelDbContextFactory(IConfiguration configuration)
        {
            _configuration = configuration;
        }

        public MarvelDbContext CreateDbContext()
        {
            var connectionString = _configuration.GetConnectionString("MarvelDb");

            var options = new DbContextOptionsBuilder<MarvelDbContext>()
                .UseSqlServer(connectionString)
                .Options;

            return new MarvelDbContext(options);
        }
    }
}