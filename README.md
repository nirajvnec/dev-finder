// File 1: IMailDlService.cs
using Marvel.OperationalServices.GenericRepository.Entities;
using Marvel.OperationalServices.GenericRepository.Interfaces;

namespace Marvel.OperationalServices.GenericRepository.Interfaces;

public interface IMailDlService : IGenericService<MailDl, int>
{
    // Inherits all methods from IGenericService<MailDl, int>
    // Can add custom methods here if needed in future
}

// File 2: MailDlValidator.cs  
using FluentValidation;
using Marvel.OperationalServices.GenericRepository.Entities;

namespace Marvel.OperationalServices.GenericRepository.Validators;

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

// File 3: MailDlService.cs
using FluentValidation;
using Microsoft.Extensions.Logging;
using Marvel.OperationalServices.GenericRepository.Entities;
using Marvel.OperationalServices.GenericRepository.Interfaces;
using Marvel.OperationalServices.GenericRepository.Services;

namespace Marvel.OperationalServices.GenericRepository.Services;

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
        // Validate before adding
        var validationResult = await _validator.ValidateAsync(mailDl);
        if (!validationResult.IsValid)
        {
            var errors = string.Join(", ", validationResult.Errors.Select(e => e.ErrorMessage));
            throw new ValidationException($"Validation failed: {errors}");
        }

        // Call the base method to do the actual add
        return await base.AddAsync(mailDl);
    }

    public override async Task<MailDl> UpdateAsync(MailDl mailDl)
    {
        // Validate before updating
        var validationResult = await _validator.ValidateAsync(mailDl);
        if (!validationResult.IsValid)
        {
            var errors = string.Join(", ", validationResult.Errors.Select(e => e.ErrorMessage));
            throw new ValidationException($"Validation failed: {errors}");
        }

        // Call the base method to do the actual update
        return await base.UpdateAsync(mailDl);
    }

    public override async Task<bool> DeleteAsync(int id)
    {
        // Check if entity exists before deleting
        var existingMailDl = await _repository.GetByIdAsync(id);
        if (existingMailDl == null)
        {
            return false;
        }

        // Call the base method to do the actual delete
        return await base.DeleteAsync(id);
    }
}

// File 4: Add FluentValidation package to .csproj
/*
Add this to Marvel.OperationalServices.GenericRepository.csproj:

<PackageReference Include="FluentValidation" Version="11.9.0" />
*/

// File 5: Registration in DI Container (for your API Program.cs)
/*
Add these registrations:

// Register validator
builder.Services.AddScoped<IValidator<MailDl>, MailDlValidator>();

// Register specific service (not generic service)
builder.Services.AddScoped<IMailDlService, MailDlService>();

// Still need repository
builder.Services.AddScoped<IGenericRepository<MailDl, int>, GenericRepository<MailDl, int>>();
*/