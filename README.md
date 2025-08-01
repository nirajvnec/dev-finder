using System.Diagnostics;

var baseDir = @"C:\UBS\Dev\mr-marvel-commentaryservice\SRC";
var slnName = "MarvelCommentaryApi";
var projects = new[]
{
    "Marvel.Commentary.APIHost",
    "Marvel.Commentary.Repositories",
    "Marvel.Commentary.RepositoriesContract",
    "Marvel.Commentary.Services",
    "Marvel.Commentary.ServicesContract",
    "Marvel.Commentary.Shared"
};

Console.WriteLine($"📁 Creating folder: {baseDir}");
Directory.CreateDirectory(baseDir);
Environment.CurrentDirectory = baseDir;

// Create solution
Run("dotnet", $"new sln -n {slnName}");

// Create projects
foreach (var proj in projects)
{
    var template = proj.Contains("APIHost") ? "webapi" : "classlib";
    Run("dotnet", $"new {template} -n {proj}");
}

// Add projects to solution
foreach (var proj in projects)
{
    var csprojPath = Path.Combine(proj, $"{proj}.csproj");
    Run("dotnet", $"sln {slnName}.sln add {csprojPath}");
}

// Add project references (APIHost depends on all others)
var apiHost = "Marvel.Commentary.APIHost";
foreach (var proj in projects.Where(p => p != apiHost))
{
    Run("dotnet", $"add {apiHost} reference {proj}");
}

Console.WriteLine("\n✅ Done creating solution and projects at:");
Console.WriteLine(baseDir);

static void Run(string fileName, string args)
{
    Console.WriteLine($"> {fileName} {args}");

    var process = new Process
    {
        StartInfo = new ProcessStartInfo
        {
            FileName = fileName,
            Arguments = args,
            RedirectStandardOutput = true,
            RedirectStandardError = true,
            UseShellExecute = false
        }
    };

    process.OutputDataReceived += (s, e) => { if (e.Data != null) Console.WriteLine(e.Data); };
    process.ErrorDataReceived += (s, e) => { if (e.Data != null) Console.Error.WriteLine(e.Data); };

    process.Start();
    process.BeginOutputReadLine();
    process.BeginErrorReadLine();
    process.WaitForExit();
}