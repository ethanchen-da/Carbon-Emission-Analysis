## 1\. Introduction

### What is the project about?

This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends.

Carbon emissions play a crucial role in the environment, accounting for over 75% of global emissions and posing a significant environmental challenge. These emissions contribute to the accumulation of greenhouse gases in the atmosphere, leading to climate change, planetary warming, and involvement in various environmental disasters.

Through this analysis, we hope to gain an understanding of the environmental impact of different industries and contribute to making informed decisions in sustainable development.

### Data Source: Where Our Data Comes From

Our dataset is compiled from publicly available data from nature.com and encompasses the product carbon footprints (PCF) for various companies. PCFs represent the greenhouse gas emissions associated with specific products, quantified in CO2 (carbon dioxide equivalent).

### Data Structure

The dataset consists of 4 tables containing information regarding carbon emissions generated during the production of goods.

![image](https://github.com/ethanchen-da/Carbon-Emission-Analysis/blob/main/Database%20diagram.png)

## 2\. Project brief

### Tables' columns description

Table '**product_emissions**'
* id: Identifier for each product emission record.
* company_id: Identifier for the company associated with the product.
* country_id: Identifier for the country where the product is being produced.
* industry_group_id: Identifier for the industry group to which the product belongs.
* year: The year in which the emissions data was recorded.
* product_name: The name of the product associated with the emissions data.
* weight_kg: The weight of the product in kilograms.
* carbon_footprint_pcf: The carbon footprint of the product, measured in CO2 equivalent.
* upstream_percent_total_pcf: The percentage of the total carbon footprint attributed to upstream activities.
* operations_percent_total_pcf: The percentage of the total carbon footprint attributed to operations.
* downstream_percent_total_pcf: The percentage of the total carbon footprint attributed to downstream activities.
 

Table '**industry_groups**'
* id: Unique identifier for each industry group.
* industry_group: The name of the industry group, categorizing businesses within similar sectors based on their products or services offered.
 

Table '**companies**'
* id: Unique identifier for each company.
* company_name: The name of the company, identifying the specific organization within the dataset.
 

Table '**countries**'
* id: Unique identifier for each country.
* country_name: The name of the country.

First, I’ll take a quick look at the data we have. Below are the first 5 rows from the product\_emissions table

***Query:***
```sql
SELECT *
FROM product_emissions
LIMIT 5;
```

 ***Results:***
| id | company\_id | country\_id | industry\_group\_id | year | product\_name | weight\_kg | carbon\_footprint\_pcf | upstream\_percent\_total\_pcf | operations\_percent\_total\_pcf | downstream\_percent\_total\_pcf |
| --- | ---------- | ---------- | ----------------- | ---- | ------------ | --------- | -------------------- | -------------------------- | ---------------------------- | ---------------------------- |
| 10056-1-2014 | 82 | 28 | 2 | 2014 | Frosted Flakes(R) Cereal | 0.7485 | 2 | 57.50 | 30.00 | 12.50 |
| 10056-1-2015 | 82 | 28 | 15 | 2015 | "Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)" | 0.7485 | 2 | 57.50 | 30.00 | 12.50 |
| 10222-1-2013 | 83 | 28 | 8 | 2013 | Office Chair | 20.68 | 73 | 80.63 | 17.36 | 2.01 |
| 10261-1-2017 | 14 | 16 | 25 | 2017 | Multifunction Printers | 110.0 | 1488 | 30.65 | 5.51 | 63.84 |
| 10261-2-2017 | 14 | 16 | 25 | 2017 | Multifunction Printers | 110.0 | 1818 | 25.08 | 4.51 | 70.41 |

After that, I’ll go through the three tables one by one: industry\_groups, companies, and countries

***Query:***
```sql
SELECT *
FROM industry_groups
LIMIT 5;
```

 ***Results:***
|id|industry_group|
|--|--------------|
|1|"Consumer Durables, Household and Personal Products"|
|2|"Food, Beverage & Tobacco"|
|3|"Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber"|
|4|"Mining - Iron, Aluminum, Other Metals"|
|5|"Pharmaceuticals, Biotechnology & Life Sciences"|

***Query:***
```sql
SELECT *
FROM companies
LIMIT 5;
```

 ***Results:***
| id | company\_name |
| --- | ------------ |
| 1 | "Autodesk, Inc." |
| 2 | "Casio Computer Co., Ltd." |
| 3 | "Cisco Systems, Inc." |
| 4 | "CNX Coal Resources, LP" |
| 5 | "Coca-Cola Enterprises, Inc." |

***Query:***
```sql
SELECT *
FROM countries
LIMIT 5;
```

 ***Result:***
| id | country\_name |
| --- | ------------ |
| 1 | Australia |
| 2 | Belgium |
| 3 | Brazil |
| 4 | Canada |
| 5 | Chile |

Before starting the analysis, I’ll clean the data first. To begin, I’ll check if there are any duplicate records

***Query:***
```sql
WITH abc AS 
(
SELECT
	id,
	COUNT(*) AS cnt
FROM 
    product_emissions
GROUP BY id
HAVING COUNT(*) > 1
)
SELECT 
    COUNT(id) AS 'Count of Duplicate ID'
FROM abc;
```

 ***Results:***

|Count of duplicate ID|
|---------------------|
|171|

### Analysis

I will begin by examining which products contribute the most to carbon emissions. This will help us identify the major drivers of emissions and set a clear direction for deeper analysis in the following steps.

***Query:***
```sql
SELECT
	pe.id AS 'Product ID',
	ROUND(AVG(carbon_footprint_pcf),2) AS 'AVG of Carbon PCF',
	REPLACE(pe.product_name,'"','') AS 'Product Name'
FROM 
	product_emissions AS pe
GROUP BY 
	pe.id,
	pe.product_name
ORDER BY AVG(carbon_footprint_pcf) DESC
LIMIT 10;
```

 ***Results:***

|Product ID|AVG of Carbon PCF|Product's Name|
|----------|-----------------|------------|
|22917-4-2015|3718044.00|Wind Turbine G128 5 Megawats|
|22917-5-2015|3276187.00|Wind Turbine G132 5 Megawats|
|22917-3-2015|1532608.00|Wind Turbine G114 2 Megawats|
|22917-2-2015|1251625.00|Wind Turbine G90 2 Megawats|
|8362-1-2016|191687.00|Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.|
|904-2-2013|167000.00|Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall|
|12134-8-2017|99075.00|TCDE|
|4235-30-2016|91000.00|Mercedes-Benz GLE (GLE 500 4MATIC)|
|20527-1-2014|87589.00|Electric Motor|
|4235-32-2016|85000.00|Mercedes-Benz S-Class (S 500)|

![image](https://docs.google.com/spreadsheets/d/e/2PACX-1vQFIh8R3iJbr63n2eMz4ErEmh7YsJXBLQoMSOCc0OnXt8NhxbWEpiG38_H54LCZm5FQLaB9t_-vHT-s/pubchart?oid=1762809453&format=image)

From the above results, I can temporarily conclude that the common characteristic of the top 10 products is that they are heavy products, large-scale industrial goods, or large vehicles, meaning that industrial products dominate emissions. It is easy to see that turbines have a high emission level, with the highest footprint, and there is also the presence of transportation vehicles. 

Next step: To clarify this issue, I will next explore the SUM of footprint of different industry groups.

***Query:***
```sql 
SELECT
	ROUND(SUM(pe.carbon_footprint_pcf),2) AS 'SUM of Carbon PCF',
	REPLACE(ig.industry_group,'"', '') AS 'Industry Group'
FROM 
	product_emissions AS pe
JOIN industry_groups AS ig ON pe.industry_group_id = ig.id
GROUP BY ig.industry_group
ORDER BY SUM(pe.carbon_footprint_pcf) DESC
LIMIT 10;
```

***Results:***
|SUM of Carbon PCF|Industry Group|
|-----------------|--------------|
|9801558.00|Electrical Equipment and Machinery|
|2582264.00|Automobiles & Components|
|577595.00|Materials|
|363776.00|Technology Hardware & Equipment|
|258712.00|Capital Goods|
|111131.00|Food, Beverage & Tobacco|
|72486.00|Pharmaceuticals, Biotechnology & Life Sciences|
|62369.00|Chemicals|
|46544.00|Software & Services|
|23017.00|Media|

![image](https://docs.google.com/spreadsheets/d/e/2PACX-1vQFIh8R3iJbr63n2eMz4ErEmh7YsJXBLQoMSOCc0OnXt8NhxbWEpiG38_H54LCZm5FQLaB9t_-vHT-s/pubchart?oid=1929394781&format=image)


The results reveal a significant gap between the first and second positions. Although the footprint of the Automobiles & Components sector is substantial, it remains far behind Electrical Equipment and Machinery, whose emissions are approximately four times higher. This indicates that the majority of emissions originate from heavy industrial sectors and large-scale equipment.

Although its emissions are not among the highest, the Pharmaceuticals, Biotechnology & Life Sciences sector illustrates a stark reality: in safeguarding human health, we continue to borrow from the planet’s own well-being. This highlights the interconnected nature of health and sustainability, reminding us that innovations aimed at saving lives must also account for their environmental impact.

To explore this further, I have decided to examine the correlation between footprint, upstream, and downstream across industry groups, in order to determine which stages actually contribute to their carbon emissions.

***Query:***
```sql
SELECT
    ROUND(SUM(pe.carbon_footprint_pcf), 2) AS `SUM of Carbon PCF`,
    REPLACE(ig.industry_group, '"','') AS `Industry Group`,
    NULLIF(pe.upstream_percent_total_pcf, 'N/a (product with insufficient stage-level data)') AS `Upstream PCF`,
    NULLIF(pe.downstream_percent_total_pcf, 'N/a (product with insufficient stage-level data)') AS `Downstream PCF`,
    NULLIF(pe.operations_percent_total_pcf, 'N/a (product with insufficient stage-level data)') AS `Operations PCF`
FROM product_emissions AS pe
JOIN industry_groups AS ig ON pe.industry_group_id = ig.id
GROUP BY ig.industry_group
ORDER BY SUM(pe.carbon_footprint_pcf) DESC
LIMIT 10;
```

***Results:***
|SUM of Carbon PCF|Industry Group|Upstream PCF|Downstream PCF|Operations PCF|
|-----------------|--------------|------------|--------------|--------------|
|9801558.00|Electrical Equipment and Machinery|6.68|91.88|1.44|
|2582264.00|Automobiles & Components|18.63|75.62|5.75|
|577595.00|Materials||||
|363776.00|Technology Hardware & Equipment|30.65|63.84|5.51|
|258712.00|Capital Goods|80.63|2.01|17.36|
|111131.00|Food, Beverage & Tobacco|57.50|12.50|30.00|
|72486.00|Pharmaceuticals, Biotechnology & Life Sciences|2.95|96.41|0.64|
|62369.00|Chemicals||||
|46544.00|Software & Services|67.61|30.17|2.22|
|23017.00|Media|64.62|34.81|0.57|


From the table data, it is clear that the footprints of industries such as Electrical Equipment and Machinery, Automobiles & Components, and Pharmaceuticals, Biotechnology & Life Sciences have a high proportion of emissions through downstream activities. In contrast, other industries primarily emit through the production of raw materials and infrastructure. Operations contribute very little to emissions, except for the Food, Beverage & Tobacco sector, which accounts for 30%.

From here, I will continue to investigate which industry has the highest upstream proportion, indicating the greatest reliance on the supply chain.

***Query:***
```sql
SELECT
	REPLACE(ig.industry_group, '"', '') AS 'Industry Group',
	pe.upstream_percent_total_pcf AS `Upstream PCF`,
	pe.downstream_percent_total_pcf AS `Downstream PCF`,
	pe.operations_percent_total_pcf AS `Operations PCF`
FROM 
	product_emissions pe
JOIN industry_groups AS ig ON pe.industry_group_id = ig.id
WHERE pe.upstream_percent_total_pcf IS NOT NULL
GROUP BY ig.industry_group
HAVING pe.upstream_percent_total_pcf NOT LIKE 'N/a%'
ORDER BY pe.upstream_percent_total_pcf  DESC
LIMIT 10;
```


***Results:***
|Industry Group|Upstream PCF|Downstream PCF|Operations PCF|
|--------------|------------|--------------|--------------|
|Containers & Packaging|87.01|0.00|12.99|
|Capital Goods|80.63|2.01|17.36|
|Retailing|78.45|16.29|5.26|
|Semiconductors & Semiconductor Equipment|72.39|0.00|27.61|
|Software & Services|67.61|30.17|2.22|
|Media|64.62|34.81|0.57|
|Electrical Equipment and Machinery|6.68|91.88|1.44|
|Food & Beverage Processing|57.50|12.50|30.00|
|Food, Beverage & Tobacco|57.50|12.50|30.00|
|Tobacco|57.14|0.00|42.86|

![image](https://docs.google.com/spreadsheets/d/e/2PACX-1vQFIh8R3iJbr63n2eMz4ErEmh7YsJXBLQoMSOCc0OnXt8NhxbWEpiG38_H54LCZm5FQLaB9t_-vHT-s/pubchart?oid=114843765&format=image)

From the data, it is evident that the industry most dependent on the supply chain is Containers & Packaging, with a clear disparity between industries with extremely high upstream ratios (87.01%) and those with very high downstream ratios (91.88%). These industries will require completely different carbon reduction strategies.

Next step: To further explore the data, I will examine which industries have the highest proportions of upstream and downstream emissions.

***Query:***
```sql
SELECT
	ROUND(SUM(pe.carbon_footprint_pcf),2) AS 'SUM of Carbon PCF',
	c.country_name AS 'Country Name',
	REPLACE(com.company_name, '"', '') AS 'Company Name'
FROM 
	product_emissions AS pe
JOIN companies AS com ON pe.company_id = com.id
JOIN countries AS c ON pe.country_id = c.id
GROUP BY com.company_name, c.country_name
ORDER BY SUM(pe.carbon_footprint_pcf) DESC
LIMIT 10;
```


***Results:***
|SUM of Carbon PCF|Country Name|Company Name|
|-----------------|------------|------------|
|9778464.00|Spain|Gamesa Corporación Tecnológica, S.A.|
|1594300.00|Germany|Daimler AG|
|655960.00|Germany|Volkswagen AG|
|212016.00|Japan|Mitsubishi Gas Chemical Company, Inc.|
|191687.00|Japan|Hino Motors, Ltd.|
|167007.00|Luxembourg|Arcelor Mittal|
|160655.00|Brazil|Weg S/A|
|137007.00|USA|General Motors Company|
|132012.00|USA|Lexmark International, Inc.|
|105600.00|Japan|Daikin Industries, Ltd.|

![image](https://docs.google.com/spreadsheets/d/e/2PACX-1vQFIh8R3iJbr63n2eMz4ErEmh7YsJXBLQoMSOCc0OnXt8NhxbWEpiG38_H54LCZm5FQLaB9t_-vHT-s/pubchart?oid=799950172&format=image)

From the table above, it is clear that countries such as Germany, the USA, and Japan have multiple companies in the Top 10 emitters, spanning diverse industries with moderate footprints. However, Gamesa from Spain, operating in heavy industry, holds the top position with a substantial lead over the second place. 

We can tentatively conclude that heavy industry overwhelmingly dominates carbon emissions.

The next step, I will analyze which country has the highest emission ratio.

***Query:***
```sql
SELECT
	ROUND(SUM(pe.carbon_footprint_pcf),2) AS 'SUM of Carbon PCF',
	c.country_name AS 'Country Name'
FROM product_emissions AS pe
JOIN countries AS c ON pe.country_id = c.id
GROUP BY c.country_name
ORDER BY SUM(carbon_footprint_pcf) DESC
LIMIT 10;
```

***Results:***

|SUM of Carbon PCF|Country Name|
|-----------------|------------|
|9786130.00|Spain|
|2251225.00|Germany|
|653237.00|Japan|
|518381.00|USA|
|186965.00|South Korea|
|169337.00|Brazil|
|167007.00|Luxembourg|
|70417.00|Netherlands|
|62875.00|Taiwan|
|24574.00|India|

![image](https://docs.google.com/spreadsheets/d/e/2PACX-1vQFIh8R3iJbr63n2eMz4ErEmh7YsJXBLQoMSOCc0OnXt8NhxbWEpiG38_H54LCZm5FQLaB9t_-vHT-s/pubchart?oid=1473321000&format=image)

Although Germany and Japan have more companies in the top emitters and a wider variety of industries, their emissions remain low compared to Spain, which is dominated by a single turbine product.

Above all, this table clearly shows that most of the top 10 countries with the highest emissions are developed nations — meaning countries with advanced industrial sectors — further reinforcing the conclusion that heavy industries account for the majority of emissions.

Next, I will conduct an analysis of the PCF trend over the years to identify any patterns or significant changes

***Query:***
```sql
SELECT
    CASE
        WHEN last_val > first_val THEN 'Increasing'
        WHEN last_val < first_val THEN 'Decreasing'
        ELSE 'Stable'
    END AS Trend
FROM (
    SELECT
        FIRST_VALUE(total_sum) OVER (ORDER BY year) AS first_val,
        LAST_VALUE(total_sum) OVER (ORDER BY year ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_val
    FROM (
        SELECT year, SUM(carbon_footprint_pcf) AS total_sum
        FROM product_emissions
        GROUP BY year
    ) yearly
) t
LIMIT 1;
```

***Results:***
|Trend|
|-----|
|Decreasing|

Although the overall trend shows a decrease in emissions, I will continue querying to explore the details further.


***Query:***
```sql
SELECT 
	pe.year AS 'Year',
	ROUND(SUM(pe.carbon_footprint_pcf),2) AS 'SUM of Carbon PCF'
FROM
	product_emissions AS pe
GROUP BY pe.year
ORDER BY pe.year, SUM(pe.carbon_footprint_pcf) DESC
;
```

***Results:***
|Year|SUM of Carbon PCF|
|----|-----------------|
|2013|503857.00|
|2014|624226.00|
|2015|10840415.00|
|2016|1640182.00|
|2017|340271.00|

![image](https://docs.google.com/spreadsheets/d/e/2PACX-1vQFIh8R3iJbr63n2eMz4ErEmh7YsJXBLQoMSOCc0OnXt8NhxbWEpiG38_H54LCZm5FQLaB9t_-vHT-s/pubchart?oid=69880907&format=image)

From the detailed data, we can observe a major spike in 2015, where PCF emissions significantly surpassed those of other years. This is likely due to a large number of products being launched or many companies being established in 2015. From here, I will proceed to analyze in more detail the number of products produced each year.

***Query:***
```sql
SELECT
	pe.year AS Year,
	COUNT(pe.id) AS 'Count of Product',
	ROUND(AVG(pe.carbon_footprint_pcf),2) AS 'AVG of footprint PCF'
FROM 
	product_emissions pe
GROUP BY pe.year 
ORDER BY pe.year ASC
;
```

***Results:***
|Year|Count of Product|AVG of footprint PCF|
|----|----------------|--------------------|
|2013|210|2399.32|
|2014|254|2457.58|
|2015|251|43188.90|
|2016|238|6891.52|
|2017|84|4050.85|

It can be observed that the average footprint in 2015 was significantly higher than in other years, and 2016 was also several times higher than the previous years. As for 2017, although there were only 84 products, they might belong to industry groups with high footprints. I will proceed to investigate this further.

According to the results, the spike in footprint in 2015 was not necessarily due to a higher number of products being produced. Therefore, I will investigate whether the industry groups that produced in 2015 differed significantly from those in other years.

***Query:***
```sql
WITH abc AS 
(
SELECT
	pe.year AS Year,
	COUNT(pe.id) AS 'Count of Product',
	REPLACE(ig.industry_group,'"','') AS Industry,
	ROUND(AVG(pe.carbon_footprint_pcf),2) AS 'AVG of footprint PCF'
FROM 
	product_emissions pe
LEFT JOIN industry_groups ig ON pe.industry_group_id = ig.id
GROUP BY 
	pe.year, 
	ig.industry_group
ORDER BY 
	pe.year ASC, 
	AVG(pe.carbon_footprint_pcf) DESC
)
SELECT
	COUNT(Industry) AS 'Count of Industry per year', 
	Industry, 
	Year
FROM abc
GROUP BY Year
;
```

***Results:***

|Count of Industry per year|Industry|Year|
|--------------------------|--------|----|
|14|Automobiles & Components|2013|
|14|Pharmaceuticals, Biotechnology & Life Sciences|2014|
|22|Electrical Equipment and Machinery|2015|
|13|Automobiles & Components|2016|
|6|Capital Goods|2017|

The data clearly shows that 2015 was the year with the highest number of industries, indicating greater diversity in sectors. This could be linked to the spike in PCF emissions. The emergence of Electrical Equipment and Machinery, along with other industries that appeared exclusively in 2015, likely contributed to the significant increase in total footprint for that year.

Following that, the number of industries decreased in 2016 and 2017, suggesting a trend where companies began to shift toward already established sectors. This indicates the start of industry consolidation, with fewer short-lived sectors appearing.
