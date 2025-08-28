using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.Threading.Tasks;
using Marvel.OperationalServices.Models;
using Marvel.OperationalServices.Repositories;

namespace Marvel.OperationalServices.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class TableEditorController : ControllerBase
    {
        private readonly ITableEditorRepository _tableEditorRepository;

        public TableEditorController(ITableEditorRepository tableEditorRepository)
        {
            _tableEditorRepository = tableEditorRepository;
        }

        // GET: api/TableEditor/tables
        [HttpGet("tables")]
        public async Task<ActionResult<IEnumerable<TableEditorTableNames>>> GetTableNames()
        {
            var result = await _tableEditorRepository.GetTableNamesAsync();
            
            if (result == null)
                return NotFound();

            return Ok(result);
        }
    }
}