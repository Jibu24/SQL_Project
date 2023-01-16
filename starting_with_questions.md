Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
```sql
SELECT * FROM
(
	SELECT  "country"
	   ,"city"
	   ,SUM("totalTransactionRevenue") AS revenue 
	FROM all_sessions
	GROUP BY ROLLUP ("country", "city")
	ORDER BY revenue desc
	) as subq
WHERE "revenue" IS NOT NULL
```



Answer: The highest level of revenues on the site came from the United States within cities San Francisco, Sunnyvale, Atlanta, Palo Alto and tel Aviv-Yafo




**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

```sql
SELECT DISTINCT * FROM 
(
	SELECT  al."country"
	   		,al."city" 
	   		,FLOOR(AVG(sr."total_ordered") OVER (PARTITION BY al."country",al."city")) AS avgProdOrdCity
	   		,FLOOR(AVG(sr."total_ordered") OVER (PARTITION BY al."country")) AS avgProdOrdCountry
	     FROM sales_report sr
	     INNER JOIN all_sessions al
	        ON sr."productSKU" = al."productSKU"
) as subq
ORDER BY country
```
Answer: There are many cities and countries queried for so I will go with an alphabetical ordering of unique countries to list 5 of them

1. Argentina had an average quantity of 20 products ordered, with Buenos Aires averaging 22
2. Australia had an average quantity of 18 products ordered, with Sydney averaging 19
3. Belgium had an average quantity of 16 products ordered, with Brussels averaging 4
4. Brazil had an average quantity of 26 products ordered, with Sao Paulo averaging 18
5. Canada had an average quantity of 19 products ordered, with Kitchner averaging 18


**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
```sql
WITH sales_CTE AS 
(
	SELECT  al."country"
	    	,al."city" 
	   		,sr."total_ordered"
			,al."v2ProductCategory"
			,al."v2ProductName"
			,ROW_NUMBER() OVER(PARTITION BY "country", "city" ORDER BY sr."total_ordered" desc)
	FROM sales_report sr
	INNER JOIN all_sessions al
		ON sr."productSKU" = al."productSKU"
	WHERE sr."total_ordered" != 0
	)

SELECT * FROM sales_CTE
WHERE row_number = 1 
```
```sql
SELECT * FROM sales_CTE
WHERE row_number = 1 
	AND "country" IN ('Argentina','Brazil','Chile','Peru')
```
```sql
SELECT * FROM sales_CTE
WHERE row_number = 1 
	AND "country" IN ('United Kingdom','United States','Canada','Germany')
```
```sql
SELECT * FROM sales_CTE
WHERE row_number = 1 
	AND "country" IN ('India', 'China', 'South Korea', 'United Arab Emirates', 'Pakistan')
```


Answer: No clear patterns were found regarding product types ordered between cities in respective countries. Then querying for product types ordered within similar geographic regions yieled no noticable patterns either. As mentioned in the answer to the question below, the highest product orders came from items within the accessories category while the least product orders came from items within apparels





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
```sql
WITH sales_CTE AS 
(
	SELECT  al."country"
	    	,al."city" 
	   		,sr."total_ordered"
			,al."v2ProductCategory"
			,al."v2ProductName"
			,ROW_NUMBER() OVER(PARTITION BY "country" ORDER BY sr."total_ordered" desc)
	FROM sales_report sr
	INNER JOIN all_sessions al
		ON sr."productSKU" = al."productSKU"
	WHERE sr."total_ordered" != 0
	)

SELECT * FROM sales_CTE
WHERE row_number = 1 
```
```sql
SELECT "country", "total_ordered", "v2ProductName" FROM sales_CTE
WHERE row_number = 1 AND "total_ordered" > 100
```

Answer: Here are the 3 most sold Products and their respective countries
1. Ballpoint LED Light Pen: Netherlands, Mexico, Malaysia, United States, Spain, Japan, Germany, South Korea, Croatia, India, Taiwan, Switzerland
2. Google 17oz Stainless Sport Bottle: Saudi Arabia, Ireland, Singapore, Canada, United Kingdom, Belgium, Kuwait
3. Leatherette Journal: Greece, United Arab Emirates, Russia, Italy, Hong Kong, Czechia

These three products were the best sellers from various different countries regardless of geographic location. It also follows that accessories were the most purchased products accross the globe while apparels were the least.


**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:
```sql
SELECT al."country"
	   ,al."city"
	   ,SUM(an."revenue") AS revenue 
FROM analytics1 an
INNER JOIN all_sessions al
	ON an."fullvisitorId" = al."fullVisitorId"
GROUP BY ROLLUP (al."country", al."city")
ORDER BY revenue desc
```

Answer: There were a lot of revenue data missing from many countries in this query, perhaps if i had more time I could have tried to fill it in using revenue data from other tables but I only realized this during the QA process. Nevertheless from the given data, the highest revenues were generated from the United States within cities like Mountain view, San Bruno, New York, Sunnyvale etc. and the lowest revenue within countries like India, Germany, Japan and Switzerland







