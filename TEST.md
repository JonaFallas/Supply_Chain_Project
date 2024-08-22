~~~ SQL
-- Total Revenue

SELECT 
	SUM(Revenue_generated) as Total_Revenue
FROM 
	supply_chain_data

~~~

| **Total_Revenue**   |
|--------------|
| 577604.81874 |






~~~ SQL
-- Revenue By Product Type 

SELECT 
	Product_Type,
	ROUND(SUM(Revenue_generated),2,1) as Revenue
FROM 
	supply_chain_data
GROUP BY
	Product_Type

~~~


| **Product_Type**   |  **Revenue** |
|--------------------|--------------|
| cosmetics	|         161521.26 |
| haircare	|         174455.39 |
| skincare	|         241628.16 |







~~~ SQL
-- Revenue grouped by location

SELECT 
	Location,
	ROUND(SUM(Revenue_generated),2,1) as Revenue
FROM 
	supply_chain_data
GROUP BY
	Location
ORDER BY 
	Revenue

~~~ 

| **Location**   |  **Revenue** |
|----------------|--------------|
|Delhi	         |   81027.7    |
|Bangalore	 |  102601.72   |
|Chennai	 |  119142.81   |
|Kolkata	 |  137077.55   |
|Mumbai	         |  137755.02   |





 
