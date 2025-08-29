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
    /// Get all rows from a given table.
    /// Pass "tableName" as "schema.table" (e.g. "reference.mail_dl").
    /// </summary>
    [HttpGet("{tableName}")]
    public async Task<IActionResult> GetTableData(string tableName)
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
            // log ex in real-world apps
            return StatusCode(500, $"Internal server error: {ex.Message}");
        }
    }
}