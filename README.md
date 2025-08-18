using System;
using System.Threading.Tasks;
using Microsoft.Data.SqlClient;
using DotNet.Testcontainers.Builders;
using DotNet.Testcontainers.Containers;

Console.WriteLine("🚀 Starting SQL Server Testcontainer...");

var sqlServerContainer = new ContainerBuilder()
    .WithImage("mcr.microsoft.com/mssql/server:2022-latest")
    .WithEnvironment("ACCEPT_EULA", "Y")
    .WithEnvironment("SA_PASSWORD", "Your_password123")
    .WithPortBinding(1433, true) // Map to random free port on host
    .WithWaitStrategy(Wait.ForUnixContainer().UntilPortIsAvailable(1433))
    .Build();

try
{
    await sqlServerContainer.StartAsync();

    var connectionString = $"Server=localhost,{sqlServerContainer.GetMappedPublicPort(1433)};" +
                           $"User Id=sa;Password=Your_password123;TrustServerCertificate=True;";

    Console.WriteLine($"✅ Container started. Connection string: {connectionString}");

    using var connection = new SqlConnection(connectionString);
    await connection.OpenAsync();
    Console.WriteLine("✅ Connected to SQL Server!");

    var createCmd = connection.CreateCommand();
    createCmd.CommandText = "CREATE TABLE Employees (Id INT PRIMARY KEY IDENTITY, Name NVARCHAR(50));";
    await createCmd.ExecuteNonQueryAsync();
    Console.WriteLine("✅ Table created");

    var insertCmd = connection.CreateCommand();
    insertCmd.CommandText = "INSERT INTO Employees (Name) VALUES ('Alice');";
    await insertCmd.ExecuteNonQueryAsync();
    Console.WriteLine("✅ Row inserted");

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