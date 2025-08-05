ALTER TABLE [reference].[workspace]
ADD [workspace_alias_name] VARCHAR(200) NULL;



console.log("🔍 Checking hierarchy nodeId:", hierarchy.nodeId);
console.log("📦 Full Threshold Data:", data.threshold);

const found = (data.threshold ?? []).some((t) => {
  console.log("  ↪️ Comparing t.nodeId:", t.nodeId, "with hierarchy.nodeId:", hierarchy.nodeId);
  return t.nodeId === hierarchy.nodeId;
});