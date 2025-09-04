// File 1: IEmailService.cs
namespace Marvel.OperationalServices.ApiHost.Services;

public interface IEmailService
{
    string? GetUserEmail();
}

// File 2: EmailService.cs
using System.Security.Claims;

namespace Marvel.OperationalServices.ApiHost.Services;

public class EmailService : IEmailService
{
    private readonly IHttpContextAccessor _httpContextAccessor;
    private readonly ILogger<EmailService> _logger;

    public EmailService(IHttpContextAccessor httpContextAccessor, ILogger<EmailService> logger)
    {
        _httpContextAccessor = httpContextAccessor;
        _logger = logger;
    }

    public string? GetUserEmail()
    {
        try
        {
            var user = _httpContextAccessor.HttpContext?.User;
            if (user?.Identity?.IsAuthenticated != true)
            {
                _logger.LogWarning("User is not authenticated");
                return null;
            }

            // Try different claim types for email
            var email = user.FindFirst(ClaimTypes.Email)?.Value ?? 
                       user.FindFirst("email")?.Value ?? 
                       user.FindFirst("preferred_username")?.Value;
            
            _logger.LogDebug("Retrieved user email: {Email}", email);
            return email;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error retrieving user email");
            return null;
        }
    }
}

// File 3: Registration in Program.cs
/*
// Add to Program.cs
builder.Services.AddHttpContextAccessor();
builder.Services.AddScoped<IEmailService, EmailService>();
*/

// File 4: Usage Example
public class EntitiesController : ControllerBase
{
    private readonly IEntityServiceFactory _entityServiceFactory;
    private readonly IEmailService _emailService;
    private readonly ILogger<EntitiesController> _logger;

    public EntitiesController(
        IEntityServiceFactory entityServiceFactory,
        IEmailService emailService,
        ILogger<EntitiesController> logger)
    {
        _entityServiceFactory = entityServiceFactory;
        _emailService = emailService;
        _logger = logger;
    }

    [HttpPost("{entityName}")]
    public async Task<ActionResult<JsonElement>> Add(string entityName, [FromBody] JsonElement entity)
    {
        try
        {
            var userEmail = _emailService.GetUserEmail();
            _logger.LogInformation("User {Email} creating {EntityName}", userEmail, entityName);
            
            var result = await _entityServiceFactory.AddAsync(entityName, entity);
            return Ok(result);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error adding {EntityName} entity", entityName);
            return StatusCode(500, "Internal server error");
        }
    }
}