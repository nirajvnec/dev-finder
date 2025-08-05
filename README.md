const found = thresholdData?.some((t) => {
    const thresholdNodeMatchesHierarchyNode = t.nodeid === hierarchy.nodeid;
    return thresholdNodeMatchesHierarchyNode;
});







UPDATE [reference].[workspace]
SET [workspace_name] = 'MTRC-MR-DEV'
WHERE [workspace_name] IN (
    'Treasury Risk', 'Credit', 'Rates', 
    'Market Risk', 'Equities', 'FX', 'Volcker'
);






DELETE FROM [reference].[workspace]
WHERE [workspace_key] >= 7;





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