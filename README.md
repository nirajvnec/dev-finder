using System;
using System.Data.SqlClient;
using DotNet.Testcontainers.Builders;
using DotNet.Testcontainers.Containers;
using DotNet.Testcontainers.Configurations;

Console.WriteLine("🚀 Starting SQL Server Testcontainer...");

// Create and configure SQL Server container
var sqlServerContainer = new TestcontainersBuilder<MsSqlTestcontainer>()
    .WithDatabase(new MsSqlTestcontainerConfiguration
    {
        Password = "Your_password123" // strong password required by SQL Server
    })
    .Build();

try
{
    // Start container
    await sqlServerContainer.StartAsync();
    Console.WriteLine($"✅ Container started. Connection string: {sqlServerContainer.ConnectionString}");

    // Connect using ADO.NET
    using var connection = new SqlConnection(sqlServerContainer.ConnectionString);
    await connection.OpenAsync();
    Console.WriteLine("✅ Successfully connected to SQL Server!");

    // Create a table
    var createCmd = connection.CreateCommand();
    createCmd.CommandText = "CREATE TABLE Employees (Id INT PRIMARY KEY IDENTITY, Name NVARCHAR(50));";
    await createCmd.ExecuteNonQueryAsync();
    Console.WriteLine("✅ Table created");

    // Insert a row
    var insertCmd = connection.CreateCommand();
    insertCmd.CommandText = "INSERT INTO Employees (Name) VALUES ('Alice');";
    await insertCmd.ExecuteNonQueryAsync();
    Console.WriteLine("✅ Row inserted");

    // Query back
    var selectCmd = connection.CreateCommand();
    selectCmd.CommandText = "SELECT Id, Name FROM Employees;";
    using var reader = await selectCmd.ExecuteReaderAsync();
    while (await reader.ReadAsync())
    {
        Console.WriteLine($"Row => {reader["Id"]}: {reader["Name"]}");
    }
}
catch (Exception ex)
{
    Console.WriteLine("❌ Error: " + ex.Message);
}
finally
{
    await sqlServerContainer.DisposeAsync();
    Console.WriteLine("🛑 Container stopped and removed.");
}