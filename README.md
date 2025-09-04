// File 1: IEmailService.cs
namespace Marvel.OperationalServices.ApiHost.Services;

public interface IEmailService
{
    Task<string?> GetUserEmailAsync();
}

// File 2: EmailService.cs
using System.IdentityModel.Tokens.Jwt;

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

    public async Task<string?> GetUserEmailAsync()
    {
        try
        {
            var request = _httpContextAccessor.HttpContext?.Request;
            
            if (request != null && request.Headers.ContainsKey("Authorization"))
            {
                var authHeader = request.Headers["Authorization"].ToString();
                
                if (!string.IsNullOrWhiteSpace(authHeader) && 
                    authHeader.StartsWith("Bearer ", StringComparison.OrdinalIgnoreCase))
                {
                    var bearerToken = authHeader.Replace("Bearer ", "", StringComparison.OrdinalIgnoreCase);
                    var handler = new JwtSecurityTokenHandler();
                    
                    if (handler.CanReadToken(bearerToken))
                    {
                        var jsonToken = handler.ReadToken(bearerToken) as JwtSecurityToken;
                        var email = jsonToken?.Claims.FirstOrDefault(c => c.Type == "upn")?.Value;
                        
                        _logger.LogDebug("Retrieved user email: {Email}", email);
                        return await Task.FromResult(email ?? "Unknown");
                    }
                }
            }
            
            _logger.LogWarning("No valid bearer token found");
            return await Task.FromResult<string?>(null);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error retrieving user email from token");
            return await Task.FromResult<string?>(null);
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