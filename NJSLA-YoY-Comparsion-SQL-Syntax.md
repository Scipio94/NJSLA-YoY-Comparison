~~~ SQL
/*ELA Overall Average YoY Change*/
CREATE TEMP TABLE t1 AS 
SELECT 
 DISTINCT SY,
  Subject,
  ROUND(AVG(Proficiency___)  OVER (),2) AS Ovr_AVG
FROM `my-data-project-36654.NJSLA_Comparison.NJSLA_Spr22_ELA`
WHERE SY IS NOT NULL
UNION ALL
SELECT 
 DISTINCT SY,
  Subject,
  ROUND(AVG(Proficiency____)  OVER (),2) AS Ovr_AVG
FROM `my-data-project-36654.NJSLA_Comparison.NJSLA_Spr23_ELA`
WHERE SY IS NOT NULL;

SELECT
  t1.SY, 
  t1.Subject,
  t1.Ovr_AVG,
  COALESCE(ROUND(t1.Ovr_Avg - LAG(t1.Ovr_Avg) OVER (ORDER BY t1.SY),2),0)
   AS YoY_Change
FROM t1
ORDER BY t1.SY;

/*YoY Change Campus ELA*/
CREATE TEMP TABLE t2 AS
SELECT
  SY, 
  Subject, 
  Grade, 
  CASE
    WHEN Grade BETWEEN 3 AND 5 THEN 'ES'
    WHEN Grade BETWEEN 6 AND 8 THEN 'MS'
    WHEN Grade > 8 THEN 'HS'
    END AS Campus,
  Proficiency___ AS Proficiency
FROM `my-data-project-36654.NJSLA_Comparison.NJSLA_Spr22_ELA`
WHERE SY IS NOT NULL
UNION ALL
SELECT
  SY, 
  Subject, 
  Grade,
   CASE
    WHEN Grade BETWEEN 3 AND 5 THEN 'ES'
    WHEN Grade BETWEEN 6 AND 8 THEN 'MS'
    WHEN Grade > 8 THEN 'HS'
    END AS Campus, 
  Proficiency____ AS Proficiency
FROM `my-data-project-36654.NJSLA_Comparison.NJSLA_Spr23_ELA`
WHERE SY IS NOT NULL;

SELECT 
  sub1.SY,
  sub1.Subject, 
  sub1.Campus, 
  sub1.Campus_Avg,
  COALESCE(ROUND(sub1.Campus_Avg - LAG(sub1.Campus_Avg) OVER (ORDER BY sub1.SY),2),0) AS YoY_Change,
  COALESCE(ROUND((sub1.Campus_Avg - LAG(sub1.Campus_Avg) OVER (ORDER BY sub1.SY))/ LAG(sub1.Campus_Avg) OVER (ORDER BY sub1.SY),2),0) AS Pct_Increase
FROM 
(SELECT 
 DISTINCT sub.SY, 
  sub.Subject, 
  sub.Campus,
  ROUND(AVG(sub.Proficiency) OVER (PARTITION BY sub.SY, sub.Campus),2) AS Campus_Avg
FROM 
(SELECT
  t2.SY, 
  t2.Subject, 
  t2.Grade, 
  t2.Campus, 
  t2.Proficiency
FROM t2) AS sub
ORDER BY sub.SY, sub.Campus) AS sub1
WHERE sub1.Campus = 'HS'
ORDER BY sub1.SY;

/*YoY Change Grade ELA*/
CREATE TEMP TABLE t3 AS
SELECT
  SY, 
  Subject, 
  Grade, 
  Proficiency___ AS Proficiency
FROM `my-data-project-36654.NJSLA_Comparison.NJSLA_Spr22_ELA`
WHERE SY IS NOT NULL
UNION ALL
SELECT
  SY, 
  Subject, 
  Grade, 
  Proficiency____ AS Proficiency
FROM `my-data-project-36654.NJSLA_Comparison.NJSLA_Spr23_ELA`
WHERE SY IS NOT NULL;

SELECT
  t3.SY, 
  t3.Subject, 
  t3.Grade, 
  t3.Proficiency, 
 COALESCE(ROUND(t3.Proficiency - LAG(t3.Proficiency) OVER (ORDER BY t3.SY),2),0) AS YoY_Change,
  COALESCE(ROUND((t3.Proficiency - LAG(t3.Proficiency) OVER (ORDER BY t3.SY)) / LAG(t3.Proficiency) OVER (ORDER BY t3.SY),2),0) AS YoY_Pct_Change
FROM t3
WHERE t3.Grade = 3 -- input different integer to filter for appropiate grade level
ORDER BY t3.SY, t3.Grade




~~~~
