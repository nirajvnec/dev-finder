// File 1: Marvel.OperationalServices.GenericRepository.csproj
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore" Version="8.0.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="8.0.0" />
    <PackageReference Include="Microsoft.Extensions.Logging.Abstractions" Version="8.0.0" />
  </ItemGroup>
  <ItemGroup>
    <ProjectReference Include="..\Marvel.OperationalServices.Models\Marvel.OperationalServices.Models.csproj" />
  </ItemGroup>
</Project>

// File 2: Interfaces/IGenericRepository.cs
namespace Marvel.OperationalServices.GenericRepository.Interfaces;

public interface IGenericRepository<TEntity, TKey> where TEntity : class
{
    Task<TEntity?> GetByIdAsync(TKey id);
    Task<IEnumerable<TEntity>> GetAllAsync();
    Task<TEntity> AddAsync(TEntity entity);
    Task<TEntity> UpdateAsync(TEntity entity);
    Task<bool> DeleteAsync(TKey id);
}

// File 3: Interfaces/IGenericService.cs
namespace Marvel.OperationalServices.GenericRepository.Interfaces;

public interface IGenericService<TEntity, TKey> where TEntity : class
{
    Task<TEntity?> GetByIdAsync(TKey id);
    Task<IEnumerable<TEntity>> GetAllAsync();
    Task<TEntity> AddAsync(TEntity entity);
    Task<TEntity> UpdateAsync(TEntity entity);
    Task<bool> DeleteAsync(TKey id);
}

// File 4: Context/IGenericDbContext.cs
using Microsoft.EntityFrameworkCore;

namespace Marvel.OperationalServices.GenericRepository.Context;

public interface IGenericDbContext
{
    DbSet<TEntity> Set<TEntity>() where TEntity : class;
    Task<int> SaveChangesAsync(CancellationToken cancellationToken = default);
}

// File 5: Context/GenericDbContext.cs
using Microsoft.EntityFrameworkCore;
using Marvel.OperationalServices.Models;

namespace Marvel.OperationalServices.GenericRepository.Context;

public class GenericDbContext : DbContext, IGenericDbContext
{
    public GenericDbContext(DbContextOptions<GenericDbContext> options) : base(options) { }

    public DbSet<MailDl> MailDls { get; set; }
    public DbSet<Product> Products { get; set; }
    public DbSet<Car> Cars { get; set; }
    public DbSet<Vehicle> Vehicles { get; set; }

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

        modelBuilder.Entity<Product>(entity =>
        {
            entity.HasKey(e => e.Id);
            entity.Property(e => e.Id).HasMaxLength(50);
            entity.Property(e => e.Name).HasMaxLength(200).IsRequired();
            entity.Property(e => e.Price).HasPrecision(18, 2);
        });

        modelBuilder.Entity<Car>(entity =>
        {
            entity.HasKey(e => e.Id);
            entity.Property(e => e.Id).HasMaxLength(50);
            entity.Property(e => e.Make).HasMaxLength(100).IsRequired();
            entity.Property(e => e.Model).HasMaxLength(100).IsRequired();
        });

        modelBuilder.Entity<Vehicle>(entity =>
        {
            entity.HasKey(e => e.Id);
            entity.Property(e => e.Id).HasMaxLength(50);
            entity.Property(e => e.Type).HasMaxLength(100).IsRequired();
            entity.Property(e => e.Brand).HasMaxLength(100).IsRequired();
        });
    }
}

// File 6: Repositories/GenericRepository.cs
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Logging;
using Marvel.OperationalServices.GenericRepository.Interfaces;
using Marvel.OperationalServices.GenericRepository.Context;

namespace Marvel.OperationalServices.GenericRepository.Repositories;

public class GenericRepository<TEntity, TKey> : IGenericRepository<TEntity, TKey> 
    where TEntity : class
{
    protected readonly IGenericDbContext _context;
    protected readonly DbSet<TEntity> _dbSet;
    protected readonly ILogger<GenericRepository<TEntity, TKey>> _logger;

    public GenericRepository(IGenericDbContext context, ILogger<GenericRepository<TEntity, TKey>> logger)
    {
        _context = context;
        _dbSet = _context.Set<TEntity>();
        _logger = logger;
    }

    public virtual async Task<TEntity?> GetByIdAsync(TKey id)
    {
        try
        {
            _logger.LogInformation("Getting {EntityType} with ID {Id}", typeof(TEntity).Name, id);
            return await _dbSet.FindAsync(id);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error getting {EntityType} with ID {Id}", typeof(TEntity).Name, id);
            throw;
        }
    }

    public virtual async Task<IEnumerable<TEntity>> GetAllAsync()
    {
        try
        {
            _logger.LogInformation("Getting all {EntityType} entities", typeof(TEntity).Name);
            return await _dbSet.ToListAsync();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error getting all {EntityType} entities", typeof(TEntity).Name);
            throw;
        }
    }

    public virtual async Task<TEntity> AddAsync(TEntity entity)
    {
        try
        {
            _logger.LogInformation("Adding new {EntityType} entity", typeof(TEntity).Name);
            var result = await _dbSet.AddAsync(entity);
            await _context.SaveChangesAsync();
            return result.Entity;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error adding {EntityType} entity", typeof(TEntity).Name);
            throw;
        }
    }

    public virtual async Task<TEntity> UpdateAsync(TEntity entity)
    {
        try
        {
            _logger.LogInformation("Updating {EntityType} entity", typeof(TEntity).Name);
            _dbSet.Update(entity);
            await _context.SaveChangesAsync();
            return entity;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error updating {EntityType} entity", typeof(TEntity).Name);
            throw;
        }
    }

    public virtual async Task<bool> DeleteAsync(TKey id)
    {
        try
        {
            _logger.LogInformation("Deleting {EntityType} with ID {Id}", typeof(TEntity).Name, id);
            var entity = await GetByIdAsync(id);
            if (entity == null) 
                return false;

            _dbSet.Remove(entity);
            await _context.SaveChangesAsync();
            return true;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error deleting {EntityType} with ID {Id}", typeof(TEntity).Name, id);
            throw;
        }
    }
}

// File 7: Services/GenericService.cs
using Microsoft.Extensions.Logging;
using Marvel.OperationalServices.GenericRepository.Interfaces;

namespace Marvel.OperationalServices.GenericRepository.Services;

public class GenericService<TEntity, TKey> : IGenericService<TEntity, TKey> 
    where TEntity : class
{
    protected readonly IGenericRepository<TEntity, TKey> _repository;
    protected readonly ILogger<GenericService<TEntity, TKey>> _logger;

    public GenericService(
        IGenericRepository<TEntity, TKey> repository,
        ILogger<GenericService<TEntity, TKey>> logger)
    {
        _repository = repository;
        _logger = logger;
    }

    public virtual async Task<TEntity?> GetByIdAsync(TKey id)
    {
        if (id == null)
            throw new ArgumentNullException(nameof(id));

        _logger.LogInformation("Service: Getting {EntityType} with ID {Id}", typeof(TEntity).Name, id);
        return await _repository.GetByIdAsync(id);
    }

    public virtual async Task<IEnumerable<TEntity>> GetAllAsync()
    {
        _logger.LogInformation("Service: Getting all {EntityType} entities", typeof(TEntity).Name);
        return await _repository.GetAllAsync();
    }

    public virtual async Task<TEntity> AddAsync(TEntity entity)
    {
        if (entity == null)
            throw new ArgumentNullException(nameof(entity));

        _logger.LogInformation("Service: Adding new {EntityType} entity", typeof(TEntity).Name);
        return await _repository.AddAsync(entity);
    }

    public virtual async Task<TEntity> UpdateAsync(TEntity entity)
    {
        if (entity == null)
            throw new ArgumentNullException(nameof(entity));

        _logger.LogInformation("Service: Updating {EntityType} entity", typeof(TEntity).Name);
        return await _repository.UpdateAsync(entity);
    }

    public virtual async Task<bool> DeleteAsync(TKey id)
    {
        if (id == null)
            throw new ArgumentNullException(nameof(id));

        _logger.LogInformation("Service: Deleting {EntityType} with ID {Id}", typeof(TEntity).Name, id);
        return await _repository.DeleteAsync(id);
    }
}

// File 8: Providers/IEntityTypeProvider.cs
namespace Marvel.OperationalServices.GenericRepository.Providers;

public interface IEntityTypeProvider
{
    Type? GetEntityType(string entityName);
    IEnumerable<string> GetRegisteredEntityNames();
    Type GetKeyType(string entityName);
}

// File 9: Providers/EntityTypeProvider.cs
using Marvel.OperationalServices.Models;

namespace Marvel.OperationalServices.GenericRepository.Providers;

public class EntityTypeProvider : IEntityTypeProvider
{
    private readonly Dictionary<string, (Type EntityType, Type KeyType)> _entityTypes;

    public EntityTypeProvider()
    {
        _entityTypes = new Dictionary<string, (Type EntityType, Type KeyType)>(StringComparer.OrdinalIgnoreCase)
        {
            { "maildl", (typeof(MailDl), typeof(int)) },
            { "product", (typeof(Product), typeof(string)) },
            { "car", (typeof(Car), typeof(string)) },
            { "vehicle", (typeof(Vehicle), typeof(string)) }
        };
    }

    public Type? GetEntityType(string entityName)
    {
        return _entityTypes.TryGetValue(entityName, out var types) ? types.EntityType : null;
    }

    public Type GetKeyType(string entityName)
    {
        return _entityTypes.TryGetValue(entityName, out var types) ? types.KeyType : typeof(string);
    }

    public IEnumerable<string> GetRegisteredEntityNames()
    {
        return _entityTypes.Keys;
    }
}

// File 10: Extensions/ServiceCollectionExtensions.cs
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.DependencyInjection;
using Marvel.OperationalServices.GenericRepository.Context;
using Marvel.OperationalServices.GenericRepository.Interfaces;
using Marvel.OperationalServices.GenericRepository.Repositories;
using Marvel.OperationalServices.GenericRepository.Services;
using Marvel.OperationalServices.GenericRepository.Providers;
using Marvel.OperationalServices.Models;

namespace Marvel.OperationalServices.GenericRepository.Extensions;

public static class ServiceCollectionExtensions
{
    public static IServiceCollection AddGenericRepository(this IServiceCollection services, string connectionString)
    {
        services.AddDbContext<GenericDbContext>(options =>
            options.UseSqlServer(connectionString));

        services.AddScoped<IGenericDbContext>(provider => 
            provider.GetRequiredService<GenericDbContext>());

        services.AddScoped<IGenericRepository<MailDl, int>, GenericRepository<MailDl, int>>();
        services.AddScoped<IGenericRepository<Product, string>, GenericRepository<Product, string>>();
        services.AddScoped<IGenericRepository<Car, string>, GenericRepository<Car, string>>();
        services.AddScoped<IGenericRepository<Vehicle, string>, GenericRepository<Vehicle, string>>();

        services.AddScoped<IGenericService<MailDl, int>, GenericService<MailDl, int>>();
        services.AddScoped<IGenericService<Product, string>, GenericService<Product, string>>();
        services.AddScoped<IGenericService<Car, string>, GenericService<Car, string>>();
        services.AddScoped<IGenericService<Vehicle, string>, GenericService<Vehicle, string>>();

        services.AddSingleton<IEntityTypeProvider, EntityTypeProvider>();

        return services;
    }
}