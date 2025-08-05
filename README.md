const matchedNodes = [];
const unmatchedHierarchyNodes = [];

data.pvReportHierarchy?.forEach((hierarchy) => {
    const match = thresholdData?.find((threshold) => {
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

// ✅ Log matched nodes (both threshold and hierarchy info)
console.log("✅ Matched Nodes (Hierarchy ↔ Threshold):", matchedNodes);

// ❌ Log unmatched hierarchy nodes
console.log("❌ Hierarchy Nodes without matching threshold:", unmatchedHierarchyNodes);