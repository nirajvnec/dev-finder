using Microsoft.AspNetCore.Mvc;
using System.Text.Json;
using Marvel.OperationalServices.ApiHost.Services;

namespace Marvel.OperationalServices.ApiHost.Controllers;

[ApiController]
[Route("api/[controller]")]
public class EntitiesController : ControllerBase
{
    private readonly IEntityServiceFactory _entityServiceFactory;
    private readonly ILogger<EntitiesController> _logger;

    public EntitiesController(
        IEntityServiceFactory entityServiceFactory,
        ILogger<EntitiesController> logger)
    {
        _entityServiceFactory = entityServiceFactory;
        _logger = logger;
    }

    [HttpGet("{entityName}/{id}")]
    public async Task<ActionResult<JsonElement>> GetById(string entityName, string id)
    {
        try
        {
            var result = await _entityServiceFactory.GetByIdAsync(entityName, id);
            if (result == null)
                return NotFound($"{entityName} with ID {id} not found");
            
            return Ok(result);
        }
        catch (ArgumentException ex)
        {
            _logger.LogWarning(ex, "Bad request for {EntityName} with ID {Id}", entityName, id);
            return BadRequest(ex.Message);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error getting {EntityName} with ID {Id}", entityName, id);
            return StatusCode(500, "Internal server error");
        }
    }

    [HttpGet("{entityName}")]
    public async Task<ActionResult<IEnumerable<JsonElement>>> GetAll(string entityName)
    {
        try
        {
            var result = await _entityServiceFactory.GetAllAsync(entityName);
            return Ok(result);
        }
        catch (ArgumentException ex)
        {
            _logger.LogWarning(ex, "Bad request for getting all {EntityName}", entityName);
            return BadRequest(ex.Message);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error getting all {EntityName} entities", entityName);
            return StatusCode(500, "Internal server error");
        }
    }

    [HttpPost("{entityName}")]
    public async Task<ActionResult<JsonElement>> Add(string entityName, [FromBody] JsonElement entity)
    {
        try
        {
            var result = await _entityServiceFactory.AddAsync(entityName, entity);
            var id = GetIdFromEntity(result);
            
            if (!string.IsNullOrEmpty(id))
            {
                return CreatedAtAction(
                    nameof(GetById), 
                    new { entityName, id }, 
                    result);
            }
            
            return Ok(result);
        }
        catch (ArgumentException ex)
        {
            _logger.LogWarning(ex, "Bad request for adding {EntityName}", entityName);
            return BadRequest(ex.Message);
        }
        catch (FluentValidation.ValidationException ex)
        {
            _logger.LogWarning(ex, "Validation failed for adding {EntityName}", entityName);
            return BadRequest(ex.Message);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error adding {EntityName} entity", entityName);
            return StatusCode(500, "Internal server error");
        }
    }

    [HttpPut("{entityName}")]
    public async Task<ActionResult<JsonElement>> Update(string entityName, [FromBody] JsonElement entity)
    {
        try
        {
            var result = await _entityServiceFactory.UpdateAsync(entityName, entity);
            return Ok(result);
        }
        catch (ArgumentException ex)
        {
            _logger.LogWarning(ex, "Bad request for updating {EntityName}", entityName);
            return BadRequest(ex.Message);
        }
        catch (FluentValidation.ValidationException ex)
        {
            _logger.LogWarning(ex, "Validation failed for updating {EntityName}", entityName);
            return BadRequest(ex.Message);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error updating {EntityName} entity", entityName);
            return StatusCode(500, "Internal server error");
        }
    }

    [HttpDelete("{entityName}/{id}")]
    public async Task<ActionResult> Delete(string entityName, string id)
    {
        try
        {
            var success = await _entityServiceFactory.DeleteAsync(entityName, id);
            if (!success)
                return NotFound($"{entityName} with ID {id} not found");
            
            return NoContent();
        }
        catch (ArgumentException ex)
        {
            _logger.LogWarning(ex, "Bad request for deleting {EntityName} with ID {Id}", entityName, id);
            return BadRequest(ex.Message);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error deleting {EntityName} with ID {Id}", entityName, id);
            return StatusCode(500, "Internal server error");
        }
    }

    [HttpGet("supported-entities")]
    public ActionResult<IEnumerable<string>> GetSupportedEntities()
    {
        try
        {
            var entities = _entityServiceFactory.GetSupportedEntityNames();
            return Ok(entities);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error getting supported entities");
            return StatusCode(500, "Internal server error");
        }
    }

    private static string GetIdFromEntity(JsonElement entity)
    {
        if (entity.TryGetProperty("mailDlKey", out JsonElement mailDlKeyElement))
            return mailDlKeyElement.ToString();
        if (entity.TryGetProperty("id", out JsonElement idElement))
            return idElement.ToString();
        if (entity.TryGetProperty("Id", out JsonElement upperIdElement))
            return upperIdElement.ToString();
        
        return string.Empty;
    }
}