What are your risk areas? Identify and describe them.
The revenue columns in the analytics table and the all_sessions table were risky areas because a lot of records had no value in them especially since the corresponding unit sold column had values in them. Also a lot of cities were missing from the dataset and a lot of these had duplicated information. These were especially risky because it might significantly overestimate the aggregated revenue when doing analysis


QA Process:
Describe your QA process and include the SQL queries used to execute it.

1. First thing I did was make sure all the tables had unique rows 

FOR EXAMPLE:
```sql 
SELECT COUNT(*) FROM analytics
```
```sql
SELECT DISTINCT * FROM analytics
```
- If the number of rows matched between these two queries then I knew all the rows were unique
- In the analytics table the row count did not match, the process of fixing it was detailed in the data cleaning section

2. The revenue column had null values where there werent supposed to be any, the following queries were used to identify and fix them
```sql
SELECT "units_sold", "unit_price", "revenue"
FROM analytics1
WHERE "units_sold" != 0 AND "unit_price" != 0 AND "revenue" = 0
```
```sql
SELECT "units_sold"
	  ,"unit_price"
	  ,(CASE 
			WHEN "revenue" = 0 THEN "units_sold" * "unit_price"
			ELSE "revenue"
		END) AS updt_revenue
FROM analytics1
```
-If I had more time I would write a query where I can load this back into the database to further "clean" the table

