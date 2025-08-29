using Microsoft.AspNetCore.Mvc;
using System.Threading.Tasks;

namespace YourNamespace.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class TableSchemaController : ControllerBase
    {
        private readonly ITableSchemaRepository _tableSchemaRepository;

        public TableSchemaController(ITableSchemaRepository tableSchemaRepository)
        {
            _tableSchemaRepository = tableSchemaRepository;
        }

        /// <summary>
        /// Gets the schema of a table by name, including metadata and database info.
        /// </summary>
        /// <param name="tableName">The name of the table to fetch schema info for.</param>
        /// <returns>List of columns with schema and metadata info.</returns>
        [HttpGet("{tableName}")]
        public async Task<IActionResult> GetTableSchema(string tableName)
        {
            if (string.IsNullOrWhiteSpace(tableName))
                return BadRequest("Table name is required.");

            try
            {
                var schema = await _tableSchemaRepository.GetTableSchemaByTableNameAsync(tableName);

                if (schema == null || !schema.Any())
                    return NotFound($"No schema found for table '{tableName}'.");

                return Ok(schema);
            }
            catch (KeyNotFoundException ex)
            {
                return NotFound(new { Message = ex.Message });
            }
            catch (Exception ex)
            {
                // Log exception details for troubleshooting
                return StatusCode(500, new { Message = "An unexpected error occurred.", Details = ex.Message });
            }
        }
    }
}