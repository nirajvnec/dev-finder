const found = (data.threshold ?? []).some((t: ThresholdData, index: number) => {
  const iterationNumber = index + 1;
  console.log(`${iterationNumber}th iteration`);
  console.log("t.nodeId:", t.nodeId);
  console.log("hierarchy.nodeId:", hierarchy.nodeId);
  console.log("hierarchy.showDownToLevel:", hierarchy.showDownToLevel);

  return t.nodeId === hierarchy.nodeId;
});