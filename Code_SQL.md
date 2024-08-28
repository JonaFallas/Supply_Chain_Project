~~~ SQL

--Insights 


-- Total Revenue

SELECT 
	SUM(Revenue_generated) as Total_Revenue
FROM 
	supply_chain_data;

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
	Product_Type;


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
	Revenue_Percentage; 

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
	Location;

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
	supply_chain_data;

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
	Product_type;

~~~

| **Product_type** |   **Profit** |
|------------------|--------------|
|cosmetics         |   146877.93  |
|haircare	   |   155278.09  |
|skincare	   |   217242.73  |

~~~ SQL




--Profit Magin

WITH Profit_Revenue_CTE AS(
SELECT 
	ROUND(SUM(Revenue_generated),2) AS Total_Revenue,
	ROUND(SUM((Revenue_generated-(Shipping_costs+Manufacturing_costs+Costs))),2) AS Net_Profit
FROM 
	supply_chain_data
)
SELECT 
    ROUND((Net_Profit / Total_Revenue) * 100,2) AS Profit_Margin
FROM 
	Profit_Revenue_CTE;
~~~


| **Profit_Margin**  |
|--------------------|
|    89.92           |


~~~ SQL




-- Coorrelation between Revenue generated and profit for each product

WITH Profit_Revenue_CTE AS(
SELECT 
	SKU,
	ROUND(SUM(Revenue_generated),2) AS Revenue,
	ROUND(SUM((Revenue_generated-(Shipping_costs+Manufacturing_costs+Costs))),2) AS Profit
FROM 
	supply_chain_data
GROUP BY 
	SKU)

--Calculating  the Pearson Correlation Coefficient
SELECT 
    (AVG(Revenue * Profit) - AVG(Revenue) * AVG(Profit)) / 
    (STDEVP(Revenue) * STDEVP(Profit)) AS Correlation_Coefficient
FROM 
    Profit_Revenue_CTE;




~~~SQL



-- Cost Analysis 

--- Top 10 products with highest manufactuiring costs

SELECT
	TOP 10
	SKU,
	Manufacturing_costs
FROM 
	supply_chain_data
ORDER BY 
	Manufacturing_costs DESC;

~~~


| **Product_type** | **SKU** | **Manufacturing_costs** |
|------------------|---------|-------------------------|
|       cosmetics  |SKU7     |	   99.4661086          |
|       cosmetics  |SKU23    |	   98.60995724         |
|       skincare   |SKU13    |	   97.82905011         |
|       haircare   |SKU83    |	   97.7305938          |
|       skincare   |SKU15    |	   97.12128175         |
|       haircare   |SKU68    |	   97.11358156         |
|       skincare   |SKU10    |	   96.52735279         |
|       skincare   |SKU42    | 	   96.42282064         |
|       skincare   |SKU41    |	   95.33206455         |
|       skincare   |SKU4     |	   92.0651606          |

~~~ SQL




-- Inspection results and avg defect rates.

SELECT 
	Inspection_results,
	COUNT(SKU)*100/(SELECT COUNT(SKU) FROM supply_chain_data) AS '%_products',
	AVG(Defect_rates) as Average_Defect_Rates
FROM 
	supply_chain_data
GROUP BY 
	Inspection_results;

~~~


| **Inspection_results** | **%_products** | **Average_Defect_Rates** |
|------------------------|----------------|--------------------------|
|Fail                    |	 36	  |  2,56930214508333        |
|Pass	                 |       23	  |  2,03904318604348        |
|Pending	         |       41	  |  2,15421777492683        |

~~~ SQL





-- Products with defect rates higher than the mean that did not pass inspections.

SELECT
	SKU,
	Supplier_name,
	Inspection_results,
	Defect_rates
FROM 
	supply_chain_data
WHERE 
	Inspection_results LIKE 'Fail'
	AND
	Defect_rates > (Select AVG(Defect_rates) AS Average
	FROM supply_chain_data
	WHERE 
	Inspection_results LIKE 'Fail')
ORDER BY 
	Defect_rates DESC;


~~~

| **SKU**  | **Supplier_name**	| **Inspection_results** | **Defect_rates** |
|----------|--------------------|------------------------|------------------|
|SKU42	   |    Supplier 5      |          Fail  	 | 4,939255289      |
|SKU65     |	Supplier 5	|          Fail	         | 4,911095955      |
|SKU50     |	Supplier 2	|          Fail        	 | 4,754800805      |
|SKU3      |	Supplier 5	|          Fail	         | 4,746648621      |
|SKU73	   |    Supplier 4	|          Fail	         | 4,620546065      |
|SKU55	   |    Supplier 2	|          Fail	         | 4,548919659      |
|SKU61	   |    Supplier 4	|          Fail          | 4,367470538      |
|SKU93	   |    Supplier 4	|          Fail 	 | 4,165781795      |
|SKU36	   |    Supplier 2	|          Fail	         | 3,805533379      |
|SKU87	   |    Supplier 3	|          Fail	         | 3,693737788      |
|SKU19	   |    Supplier 4	|          Fail	         | 3,646450865      |
|SKU33	   |    Supplier 5	|          Fail 	 | 3,541046012      |
|SKU66	   |    Supplier 5	|          Fail	         | 3,448063288      |
|SKU97	   |    Supplier 4	|          Fail	         | 3,376237835      |
|SKU4	   |    Supplier 1	|          Fail    	 | 3,145579523      |
|SKU44	   |    Supplier 2	|          Fail 	 | 2,96262632       |
|SKU27	   |    Supplier 5	|          Fail 	 | 2,864667838      |
|SKU60     |	Supplier 4	|          Fail	         | 2,853090617      |
|SKU80	   |    Supplier 3	|          Fail	         | 2,849662199      |
|SKU5	   |    Supplier 4	|          Fail          | 2,779193512      |
|SKU22	   |    Supplier 4	|          Fail 	 | 2,591275473      |

~~~ SQL




-- Inspections results grouped by Supplier

WITH Products_by_Supplier AS (
    SELECT 
        Supplier_name,
        COUNT(SKU) AS NumOfProds
    FROM 
        supply_chain_data 
    GROUP BY
        Supplier_name
),
Inspections_by_Supplier AS (
    SELECT
        Supplier_name,
        COUNT(*) AS Failed_inspections,
        SUM(Defect_rates) AS Sum_Rates
    FROM 
        supply_chain_data
    WHERE 
        Inspection_results LIKE 'Fail'
    GROUP BY 
        Supplier_name
)
SELECT
    i.Supplier_name,
	p.NumOfProds AS Products,
    i.Failed_inspections AS Failed_inspections,
    (i.Failed_inspections * 100.0 / p.NumOfProds) AS Percentage_Failures,
    i.Sum_rates AS Total_Defect_Rates
FROM 
    Products_by_Supplier p
JOIN 
    Inspections_by_Supplier i
ON 
    p.Supplier_name = i.Supplier_name
ORDER BY Percentage_Failures DESC;


~~~

| **Supplier_name** | **Products** | **Failed_inspections** | **Percentage_Failures** | **Total_Defect_Rates** |
|-------------------|--------------|------------------------|-------------------------|------------------------|
|Supplier 4         | 	18	   |          12	    |       66.666666666666   |	      31,401392621     |
|Supplier 5	    |   18	   |           7	    |       38.888888888888   |       25,825206003     |
|Supplier 2         |	22         |           8	    |       36.363636363636   |       21,551460071     |
|Supplier 1	    |   27	   |           6	    |       22.222222222222   |       5,443043821      |
|Supplier 3	    |   15	   |           3	    |       20.000000000000   |       8,273774707      |


~~~ SQL




--Manufacturing Costs by Supplier

SELECT
	Supplier_name,
	ROUND(SUM(Manufacturing_costs),2) AS ManufacturingCosts
FROM
	supply_chain_data
GROUP BY 
	Supplier_name

~~~

|Supplier_name | **ManufacturingCosts** |
|--------------|------------------------|
|Supplier 1    |	1221,86         |
|Supplier 2    |        915,7           |
|Supplier 3    |        654,51          |
|Supplier 4    |        1128,78         |
|Supplier 5    |        805,83          |
