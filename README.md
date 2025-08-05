UPDATE [reference].[workspace]
SET [workspace_alias_name] = [workspace_name]
WHERE [workspace_key] IN (7, 8, 9, 10, 11, 12, 19);



ALTER TABLE [reference].[workspace]
ADD [workspace_alias_name] VARCHAR(200) NULL;



console.log("🔍 Checking hierarchy nodeId:", hierarchy.nodeId);
console.log("📦 Full Threshold Data:", data.threshold);

const found = (data.threshold ?? []).some((t) => {
  console.log("  ↪️ Comparing t.nodeId:", t.nodeId, "with hierarchy.nodeId:", hierarchy.nodeId);
  return t.nodeId === hierarchy.nodeId;
});