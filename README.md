using Microsoft.AspNetCore.Mvc;

[ApiController]
[Route("api/[controller]")]
public class TableDataController : ControllerBase
{
    private readonly ITableEditorRepository _repository;

    public TableDataController(ITableEditorRepository repository)
    {
        _repository = repository;
    }

    /// <summary>
    /// Get all rows from a given table (schema.table format).
    /// Example: GET /api/tabledata/tableColumnData/reference.mail_dl
    /// </summary>
    [HttpGet("tableColumnData/{tableName}")]
    public async Task<IActionResult> GetTableColumnData(string tableName)
    {
        try
        {
            var data = await _repository.GetTableDataByTableNameAsync(tableName);

            if (data == null || !data.Any())
                return NotFound($"No data found in table {tableName}");

            return Ok(data);
        }
        catch (ArgumentException ex)
        {
            return BadRequest(ex.Message);
        }
        catch (Exception ex)
        {
            // Ideally log exception
            return StatusCode(500, $"Internal server error: {ex.Message}");
        }
    }
}