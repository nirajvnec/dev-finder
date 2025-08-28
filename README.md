using System.Collections.Generic;
using System.Threading.Tasks;
using YourNamespace.Models;

namespace YourNamespace.Repositories
{
    public interface ITableEditorRepository
    {
        // Get list of all table names
        Task<IEnumerable<TableEditorTableNames>> GetTableNamesAsync();

        // Get schema (columns info) for a table
        Task<IEnumerable<TableEditorColumnsInfo>> GetTableSchemaByTableNameAsync(string tableName);

        // Get table data by table name
        Task<IEnumerable<TableEditorDataRow>> GetTableDataByTableNameAsync(string tableName);

        // Create a new row in a table
        Task<bool> CreateRowAsync(string tableName, Dictionary<string, object?> rowData);

        // Edit/update an existing row
        Task<bool> EditRowAsync(string tableName, Dictionary<string, object?> rowData);

        // Delete a row
        Task<bool> DeleteRowAsync(string tableName, Dictionary<string, object?> keyData);

        // Save all changes in bulk (create, update, delete)
        Task<bool> SaveAllAsync(string tableName, IEnumerable<Dictionary<string, object?>> rowsData);
    }
}