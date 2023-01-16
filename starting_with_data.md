Question 1: Which month produced the highest revenue and which month produced the least throughout the years

SQL Queries:
```sql
WITH monthly_revenue AS
(
	SELECT EXTRACT(MONTH FROM "date") AS "month"
	       ,"revenue" 
	FROM analytics1
			WHERE "revenue" != 0
	)
SELECT "month"
	   ,SUM("revenue") AS "revenue"
FROM monthly_revenue
GROUP BY "month"
ORDER BY SUM("revenue") desc
```

Answer: July was the most revenue generating month, while August was the least



Question 2: How did the revenue revenue generated thoughout the years compare

SQL Queries:
```sql
WITH yearly_revenue AS
(
	SELECT EXTRACT(YEAR FROM "date") AS "year"
		   ,SUM("totalTransactionRevenue") AS "revenue"
		   ,ROW_NUMBER() OVER() AS rn
	FROM all_sessions
		WHERE "totalTransactionRevenue" != 0
	GROUP BY "year"
	)
SELECT yr1."year"
	  ,yr1."revenue"
	  ,(yr1."revenue" - yr2."revenue")/yr2."revenue" *100 AS pctChng
FROM yearly_revenue yr1	
LEFT JOIN yearly_revenue yr2
	ON yr1.rn = yr2.rn+1
```

Answer: There was a 122.26% increase in revenue generated from 2016 to 2017



Question 3: Is there a relationship between page views and revenue?

SQL Queries:
```sql
SELECT DISTINCT * FROM 
(
	SELECT "fullvisitorId"
	 	  ,"pageviews"
	      ,SUM("revenue") OVER(PARTITION BY "fullvisitorId", "pageviews") AS revenue
	FROM analytics1
	ORDER BY "revenue" desc) as subq
WHERE "revenue" != 0
ORDER BY "pageviews" desc
```

Answer: Looking at the query a general increase in revenue as page views per customer increased. This information could be useful in strategic ad placement. Higher page views could also mean they were existing customers, so any sort of marketing to retain them might prove to be even more beneficial



Question 4: How many of the purchasing customers are existing customers and how many are new?

SQL Queries:
```sql
SELECT COUNT(DISTINCT "fullvisitorId")
FROM analytics1
WHERE revenue != 0
```
```sql
SELECT COUNT(*) FROM
(
	SELECT "fullvisitorId", COUNT(*)
	FROM analytics1
		WHERE revenue != 0
	GROUP BY "fullvisitorId"
		HAVING COUNT(*) > 1
	) as subq
```
Answer: There were 5799 unique visitorIds with 2800 of them having more than one purchase



Question 5: 

SQL Queries:

Answer:
