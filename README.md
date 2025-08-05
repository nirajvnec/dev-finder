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