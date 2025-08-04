fix/populate-approved-by-report-status


fix: populate approvedBy, reportStatus, and reportStatusName in API response

Previously, the API response was missing approvedBy and report status details.
This commit ensures that approvedBy, reportStatus, and reportStatusName are correctly populated in the response.




{
  "thresholdKey": 0,
  "nodeLevel": 0,
  "nodeLevelName": "UBS Group",
  "nodeId": 0,
  "nodeName": "UBS Group",
  "comment": "Test 141",
  "createdBy": "",
  "isOverrideAll": false,
  "measureId": 20,
  "measureName": "CVA (10 Day 99% ETL)",
  "standardDeviation": 0,
  "standardDeviationCoeff": 0,
  "thresholdValue": 1
}






{
  "thresholdKey": 0,
  "nodeLevel": 0,
  "nodeLevelName": "UBS Group",
  "nodeId": 0,
  "nodeName": "UBS Group",
  "comment": "Test 141",
  "createdAt": "2025-08-04T12:00:00Z",
  "isOverrideAll": false,
  "measureId": 20,
  "measureName": "CVA (10 Day 99% ETL)",
  "thresholdValue": 1,
  "standardDeviation": 0,
  "standardDeviationCoeff": 0
}




// Apply StandardDeviationCoeff default value if null or zero

if ((threshold.StandardDeviationCoeff ?? 0) == 0)
{
    threshold.StandardDeviationCoeff = 1;
}


StandardDeviationCoeff to 1 if null or zero in threshold payload



git checkout -b feature/default-standard-deviation-coeff
