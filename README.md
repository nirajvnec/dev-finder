const found = (data.threshold ?? []).some((t: ThresholdData) => {
  console.log("t.nodeid:", t.nodeid);
  console.log("hierarchy.nodeid:", hierarchy.nodeid);
  console.log("hierarchy.showDownToLevel:", hierarchy.showDownToLevel);
  return t.nodeid === hierarchy.nodeid;
});