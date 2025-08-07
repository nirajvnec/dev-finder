const found = (data.threshold || []).some((t: ThresholdData) => {
  console.log("t.nodeid:", t.nodeid);
  console.log("hierarchy.nodeid:", hierarchy.nodeid);
  console.log("hierarchy.showDownToLevel:", hierarchy.showDownToLevel);
  return t.nodeid === hierarchy.nodeid;
});






Hi Team,
Hi Raja Gopal,

Could you please deploy the latest changes I made yesterday for the Workspace service? These updates were requested by Rajiv and have already been merged into the development branch.

I believe Nilian will need these changes in the QA environment.

Also, just to inform you—I’ll be on sick leave today.





const matchedNodes: any[] = [];
const unmatchedHierarchyNodes: any[] = [];

data.pvReportHierarchy?.forEach((hierarchy) => {
  const match = data.threshold?.find((threshold) => {
    const thresholdNodeMatchesHierarchyNode = threshold.nodeid === hierarchy.nodeid;
    return thresholdNodeMatchesHierarchyNode;
  });

  if (match) {
    matchedNodes.push({
      hierarchyNodeId: hierarchy.nodeid,
      hierarchyNodeName: hierarchy.nodename,
      thresholdNodeId: match.nodeid,
      thresholdMeasureId: match.measureid
    });
  } else {
    unmatchedHierarchyNodes.push({
      hierarchyNodeId: hierarchy.nodeid,
      hierarchyNodeName: hierarchy.nodename
    });
  }
});

console.log("✅ Matched Nodes (Hierarchy ↔ Threshold):");
console.table(matchedNodes);

console.log("❌ Hierarchy Nodes without matching threshold:");
console.table(unmatchedHierarchyNodes);