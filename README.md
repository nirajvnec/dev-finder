// File 1: MailDlValidator.cs
using FluentValidation;
using Marvel.OperationalServices.Repositories.Entities;

namespace Marvel.OperationalServices.Business.Validators;

public class MailDlValidator : AbstractValidator<MailDl>
{
    public MailDlValidator()
    {
        RuleFor(x => x.MailDlName)
            .NotEmpty()
            .WithMessage("Mail DL name is required")
            .MaximumLength(255)
            .WithMessage("Mail DL name cannot exceed 255 characters");

        RuleFor(x => x.CreatedBy)
            .NotEmpty()
            .WithMessage("Created by is required")
            .MaximumLength(100)
            .WithMessage("Created by cannot exceed 100 characters");

        RuleFor(x => x.CreatedAt)
            .NotEmpty()
            .WithMessage("Created at is required")
            .LessThanOrEqualTo(DateTime.UtcNow)
            .WithMessage("Created at cannot be in the future");
    }
}

// File 2: IMailDlService.cs
using Marvel.OperationalServices.Repositories.Entities;

namespace Marvel.OperationalServices.Business.Interfaces;

public interface IMailDlService : IGenericService<MailDl, int>
{
}

// File 3: MailDlService.cs
using FluentValidation;
using Microsoft.Extensions.Logging;
using Marvel.OperationalServices.Business.Interfaces;
using Marvel.DataHub.Repositories.Interfaces;
using Marvel.OperationalServices.Repositories.Entities;

namespace Marvel.OperationalServices.Business.Services;

public class MailDlService : GenericService<MailDl, int>, IMailDlService
{
    private readonly IValidator<MailDl> _validator;

    public MailDlService(
        IGenericRepository<MailDl, int> repository,
        ILogger<GenericService<MailDl, int>> logger,
        IValidator<MailDl> validator)
        : base(repository, logger)
    {
        _validator = validator;
    }

    public override async Task<MailDl> AddAsync(MailDl mailDl)
    {
        var validationResult = await _validator.ValidateAsync(mailDl);
        if (!validationResult.IsValid)
        {
            var errors = string.Join(", ", validationResult.Errors.Select(e => e.ErrorMessage));
            throw new ValidationException($"Validation failed: {errors}");
        }

        return await base.AddAsync(mailDl);
    }

    public override async Task<MailDl> UpdateAsync(MailDl mailDl)
    {
        var validationResult = await _validator.ValidateAsync(mailDl);
        if (!validationResult.IsValid)
        {
            var errors = string.Join(", ", validationResult.Errors.Select(e => e.ErrorMessage));
            throw new ValidationException($"Validation failed: {errors}");
        }

        return await base.UpdateAsync(mailDl);
    }

    public override async Task<bool> DeleteAsync(int id)
    {
        var existingMailDl = await _repository.GetByIdAsync(id);
        if (existingMailDl == null)
        {
            return false;
        }

        return await base.DeleteAsync(id);
    }
}

// File 4: Updated Program.cs DI Registration
/*
Add these lines to your Program.cs:

// Add FluentValidation
builder.Services.AddScoped<IValidator<MailDl>, MailDlValidator>();

// Replace the generic service registration with specific service
// Remove this line:
// builder.Services.AddScoped<IGenericService<MailDl, int>, GenericService<MailDl, int>>();

// Add this instead:
builder.Services.AddScoped<IMailDlService, MailDlService>();

// Keep the repository registration:
builder.Services.AddScoped<IGenericRepository<MailDl, int>, GenericRepository<MailDl, int>>();

// Keep other registrations:
builder.Services.AddScoped<IEntityServiceFactory, EntityServiceFactory>();
builder.Services.AddSingleton<IEntityTypeProvider, EntityTypeProvider>();
*/