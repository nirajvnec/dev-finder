var workspaceNames = await context.WorkspaceRoleMappings
    .Where(mapping => roleIds.Contains(mapping.RoleId))
    .Select(mapping => new 
    { 
        mapping.WorkspaceKey, 
        mapping.WorkspaceAliasName 
    })
    .Distinct()
    .ToListAsync();