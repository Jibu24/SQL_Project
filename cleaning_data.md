What issues will you address by cleaning the data?

Looking at the data across the 5 tables, I found that some of the numeric data was difficult to make sense of because of improper formatting or too many large numbers just floating around. Furthermore the analytics table had too many duplicated rows and some of the tables had completely empty columns with no data in them




Queries:
Below, provide the SQL queries you used to clean your data.

1) The following query I will be removing duplicate rows from the analytics table

```sql
CREATE TABLE IF NOT EXISTS public.analytics1
(
    "visitNumber" integer,
    "visitId" integer,
    "visitStartTime" integer,
    date integer,
    "fullvisitorId" real,
    userid integer,
    "channelGrouping" character varying(100) COLLATE pg_catalog."default",
    "socialEngagementType" character varying(100) COLLATE pg_catalog."default",
    units_sold integer,
    pageviews integer,
    timeonsite integer,
    bounces integer,
    revenue real,
    unit_price real
)

TABLESPACE pg_default;

ALTER TABLE IF EXISTS public.analytics1
    OWNER to postgres;
```
```sql
INSERT INTO public.analytics1
SELECT DISTINCT * FROM analytics
```
```sql

ALTER TABLE analytics1 ADD COLUMN id SERIAL PRIMARY KEY;
```
2. The following queries I used to delete columns that had no data

```sql
ALTER TABLE analytics1 DROP COLUMN "userid”;
```
```sql
ALTER TABLE all_sessions DROP “itemRevenue"
```
```sql
ALTER TABLE all_sessions DROP "itemQuantity"
```
3. The query below was used to fill in some of the null values
```sql
UPDATE all_sessions
SET "transactions" = 0,
	"totalTransactionRevenue" = 0
WHERE "transactions" IS NULL
```
```sql
UPDATE analytics1
SET "units_sold" = 0
WHERE "units_sold" IS NULL
```
```sql
UPDATE analytics1
SET "bounces" = 0
WHERE "bounces" IS NULL
```
```sql
UPDATE analytics1
SET "revenue" = 0
WHERE "revenue" IS NULL
```
```sql
UPDATE sales_report
SET "ratio" = 0.0000
WHERE "ratio" IS NULL
```
4. The below query was used to make some of the columns more readable

```sql
UPDATE sales_report
SET "ratio" = ROUND("ratio", 4);
```
```sql
UPDATE analytics1
SET "unit_price" = "unit_price"/1000000
```
```sql
UPDATE all_sessions
SET "productPrice" = "productPrice"/1000000,
	"productRevenue" = "productRevenue”/1000000
 ```










