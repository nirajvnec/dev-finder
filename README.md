using System;
using System.Diagnostics;
using System.IO;

string workingDir = @"c:\UBS\Dev\mr-marvel-commentaryservice";

// Create the directory if it doesn't exist
Directory.CreateDirectory(workingDir);

// List of creation commands using C# 12 collection expression
var creationCommands = 
[
    "new sln -n MarvelCommentaryApi",
    "new webapi -n Marvel.Commentary.APIHost",
    "new classlib -n Marvel.Commentary.Repositories",
    "new classlib -n Marvel.Commentary.RepositoriesContract",
    "new classlib -n Marvel.Commentary.Services",
    "new classlib -n Marvel.Commentary.ServicesContract",
    "new classlib -n Marvel.Commentary.Shared"
];

foreach (var cmd in creationCommands)
{
    RunDotnetCommand(workingDir, cmd);
}

// List of add commands using C# 12 collection expression
var addCommands = 
[
    "sln MarvelCommentaryApi.sln add Marvel.Commentary.APIHost/Marvel.Commentary.APIHost.csproj",
    "sln MarvelCommentaryApi.sln add Marvel.Commentary.Repositories/Marvel.Commentary.Repositories.csproj",
    "sln MarvelCommentaryApi.sln add Marvel.Commentary.RepositoriesContract/Marvel.Commentary.RepositoriesContract.csproj",
    "sln MarvelCommentaryApi.sln add Marvel.Commentary.Services/Marvel.Commentary.Services.csproj",
    "sln MarvelCommentaryApi.sln add Marvel.Commentary.ServicesContract/Marvel.Commentary.ServicesContract.csproj",
    "sln MarvelCommentaryApi.sln add Marvel.Commentary.Shared/Marvel.Commentary.Shared.csproj"
];

foreach (var cmd in addCommands)
{
    RunDotnetCommand(workingDir, cmd);
}

Console.WriteLine("Solution and projects created successfully in " + workingDir);

static void RunDotnetCommand(string workingDirectory, string arguments)
{
    var processInfo = new ProcessStartInfo
    {
        FileName = "dotnet",
        Arguments = arguments,
        WorkingDirectory = workingDirectory,
        RedirectStandardOutput = true,
        RedirectStandardError = true,
        UseShellExecute = false,
        CreateNoWindow = true
    };

    using (var process = Process.Start(processInfo))
    {
        process.WaitForExit();

        if (process.ExitCode != 0)
        {
            string error = process.StandardError.ReadToEnd();
            throw new Exception($"Command 'dotnet {arguments}' failed with error: {error}");
        }
    }
}