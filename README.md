using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
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
        /// Get schema details for a specific table.
        /// </summary>
        /// <param name="tableName">The table name.</param>
        /// <returns>Enumerable of TableEditorColumnInfo</returns>
        [HttpGet("{tableName}")]
        public async Task<ActionResult<IEnumerable<TableEditorColumnInfo>>> GetTableSchema(string tableName)
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
                return NotFound(ex.Message);
            }
            catch (Exception ex)
            {
                return StatusCode(500, $"Internal server error: {ex.Message}");
            }
        }
    }
}