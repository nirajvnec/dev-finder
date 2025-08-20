var workspaceNames = await context.WorkspaceRoleMappings
    .Where(mapping => roleIds.Contains(mapping.RoleId))
    .Select(mapping => new 
    { 
        mapping.WorkspaceKey, 
        mapping.WorkspaceAliasName 
    })
    .Distinct()
    .ToListAsync();



List<WorkspaceResponse> workspaces = new();

workspaceNames.Select((item, index) => new WorkspaceResponse
{
    DisplayValue = item.WorkspaceKey.ToString(),   // 👈 use WorkspaceKey here
    DisplayText  = item.WorkspaceAliasName,
    Index        = index + 1                       // optional if you need ordering
})
.ToList()
.ForEach(response => workspaces.Add(response));