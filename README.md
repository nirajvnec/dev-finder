WITH RankedGroups AS (
  SELECT *,
         ROW_NUMBER() OVER (PARTITION BY business_group_name ORDER BY business_group_id) AS rn
  FROM [reference].[business_hierarchy]
)
SELECT *
FROM RankedGroups
WHERE rn = 1;