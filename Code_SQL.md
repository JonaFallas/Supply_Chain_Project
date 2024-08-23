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

With CTE_revenue_location  

AS ( 

SELECT  

Location, 

ROUND(SUM(Revenue_generated),2,1) as Revenue 

FROM  

supply_chain_data 

GROUP BY 

Location 

) 

  

Select 

Location, 

Revenue, 

ROUND(Revenue / SUM(Revenue) OVER (),3,1) * 100 AS Revenue_Percentage 

FROM 

CTE_revenue_location  

ORDER BY  

Revenue_Percentage 


~~~  

| **Location**   |  **Revenue** | **Percentage** |
|----------------|--------------|----------------|  
|Delhi	         |   81027.7    |     14         |
|Bangalore	 |  102601.72   |     17.7       |
|Chennai	 |  119142.81   |     20.6       |
|Kolkata	 |  137077.55   |     23.7       |
|Mumbai	         |  137755.02   |     23.8       |





~~~ SQL 


--Profit by Location

SELECT
	ROUND(SUM((Revenue_generated-(Shipping_costs+Manufacturing_costs+Costs))),2) AS Profit
FROM
	supply_chain_data
GROUP BY 
	Location

~~~

| **Location**   |  **Profit** |
|----------------|-------------|           
|Bangalore	 |90821.62     |
|Chennai	 |105591.12    |
|Delhi	         |71994.88     |
|Kolkata	 |123605.19    |
|Mumbai	         |127385.94    |




~~~ SQL


--Total Profit

SELECT
	ROUND(SUM((Revenue_generated-(Shipping_costs+Manufacturing_costs+Costs))),2) AS Total_Profit
FROM
	supply_chain_data



~~~

| **Total_Profit** |
|------------------|
|    519398.76     |








~~~SQL

--Profit by Product Type

SELECT
	Product_type,
	ROUND(SUM((Revenue_generated-(Shipping_costs+Manufacturing_costs+Costs))),2) AS Profit
FROM
	supply_chain_data
GROUP BY 
	Product_type


~~~



| **Product_type** |   **Profit** |
|------------------|--------------|
|cosmetics         |   146877.93  |
|haircare	   |   155278.09  |
|skincare	   |   217242.73  |




