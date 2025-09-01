using FluentValidation;

public class GenericService : IGenericService
{
    private readonly IRepositoryFactory _repositoryFactory;
    private readonly IEntityTypeResolver _entityTypeResolver;
    private readonly IServiceProvider _serviceProvider;

    public GenericService(
        IRepositoryFactory repositoryFactory,
        IEntityTypeResolver entityTypeResolver,
        IServiceProvider serviceProvider)
    {
        _repositoryFactory = repositoryFactory;
        _entityTypeResolver = entityTypeResolver;
        _serviceProvider = serviceProvider;
    }

    public async Task<object?> AddAsync(string entityName, JsonElement data)
    {
        // Step 1: Resolve entity type
        var entityType = _entityTypeResolver.ResolveEntityType(entityName);
        if (entityType == null)
            throw new ArgumentException($"Entity type '{entityName}' not found.");

        // Step 2: Deserialize
        var entity = JsonSerializer.Deserialize(data.GetRawText(), entityType);
        if (entity == null)
            throw new ArgumentException("Invalid entity payload.");

        // Step 3: Validation
        var validatorType = typeof(IValidator<>).MakeGenericType(entityType);
        var validator = _serviceProvider.GetService(validatorType) as IValidator;
        if (validator != null)
        {
            var context = new ValidationContext<object>(entity);
            var validationResult = await validator.ValidateAsync(context);

            if (!validationResult.IsValid)
            {
                return new
                {
                    Success = false,
                    Errors = validationResult.Errors.Select(e => e.ErrorMessage).ToList()
                };
            }
        }

        // Step 4: Repository
        var repo = _repositoryFactory.CreateRepository(entityType);

        // Step 5: Add using ReflectionHelper
        var addedEntity = await ReflectionHelper.InvokeGenericAsync(repo, "AddAsync", new[] { entity });

        return addedEntity;
    }
}