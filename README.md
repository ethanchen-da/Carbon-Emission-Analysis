## 1\. Introduction

### What is the project about?

This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends.

Carbon emissions play a crucial role in the environment, accounting for over 75% of global emissions and posing a significant environmental challenge. These emissions contribute to the accumulation of greenhouse gases in the atmosphere, leading to climate change, planetary warming, and involvement in various environmental disasters.

Through this analysis, we hope to gain an understanding of the environmental impact of different industries and contribute to making informed decisions in sustainable development.

### Data Source: Where Our Data Comes From

Our dataset is compiled from publicly available data from nature.com and encompasses the product carbon footprints (PCF) for various companies. PCFs represent the greenhouse gas emissions associated with specific products, quantified in CO2 (carbon dioxide equivalent).

### Data Structure

The dataset consists of 4 tables containing information regarding carbon emissions generated during the production of goods.



## 2\. Project brief

### Tables' columns description

Table '**product_emissions**'
id: Identifier for each product emission record.
company_id: Identifier for the company associated with the product.
country_id: Identifier for the country where the product is being produced.
industry_group_id: Identifier for the industry group to which the product belongs.
year: The year in which the emissions data was recorded.
product_name: The name of the product associated with the emissions data.
weight_kg: The weight of the product in kilograms.
carbon_footprint_pcf: The carbon footprint of the product, measured in CO2 equivalent.
upstream_percent_total_pcf: The percentage of the total carbon footprint attributed to upstream activities.
operations_percent_total_pcf: The percentage of the total carbon footprint attributed to operations.
downstream_percent_total_pcf: The percentage of the total carbon footprint attributed to downstream activities.
 

Table '**industry_groups**'
id: Unique identifier for each industry group.
industry_group: The name of the industry group, categorizing businesses within similar sectors based on their products or services offered.
 

Table '**companies**'
id: Unique identifier for each company.
company_name: The name of the company, identifying the specific organization within the dataset.
 

Table '**countries**'
id: Unique identifier for each country.
country_name: The name of the country.

First, I’ll take a quick look at the data we have. Below are the first 5 rows from the product\_emissions table

| id | company\_id | country\_id | industry\_group\_id | year | product\_name | weight\_kg | carbon\_footprint\_pcf | upstream\_percent\_total\_pcf | operations\_percent\_total\_pcf | downstream\_percent\_total\_pcf |
| --- | ---------- | ---------- | ----------------- | ---- | ------------ | --------- | -------------------- | -------------------------- | ---------------------------- | ---------------------------- |
| 10056-1-2014 | 82 | 28 | 2 | 2014 | Frosted Flakes(R) Cereal | 0.7485 | 2 | 57.50 | 30.00 | 12.50 |
| 10056-1-2015 | 82 | 28 | 15 | 2015 | "Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)" | 0.7485 | 2 | 57.50 | 30.00 | 12.50 |
| 10222-1-2013 | 83 | 28 | 8 | 2013 | Office Chair | 20.68 | 73 | 80.63 | 17.36 | 2.01 |
| 10261-1-2017 | 14 | 16 | 25 | 2017 | Multifunction Printers | 110.0 | 1488 | 30.65 | 5.51 | 63.84 |
| 10261-2-2017 | 14 | 16 | 25 | 2017 | Multifunction Printers | 110.0 | 1818 | 25.08 | 4.51 | 70.41 |

After that, I’ll go through the three tables one by one: industry\_groups, companies, and countries

|id|industry_group|
|--|--------------|
|1|"Consumer Durables, Household and Personal Products"|
|2|"Food, Beverage & Tobacco"|
|3|"Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber"|
|4|"Mining - Iron, Aluminum, Other Metals"|
|5|"Pharmaceuticals, Biotechnology & Life Sciences"|


| id | company\_name |
| --- | ------------ |
| 1 | "Autodesk, Inc." |
| 2 | "Casio Computer Co., Ltd." |
| 3 | "Cisco Systems, Inc." |
| 4 | "CNX Coal Resources, LP" |
| 5 | "Coca-Cola Enterprises, Inc." |

| id | country\_name |
| --- | ------------ |
| 1 | Australia |
| 2 | Belgium |
| 3 | Brazil |
| 4 | Canada |
| 5 | Chile |

Before starting the analysis, I’ll clean the data first. To begin, I’ll check if there are any duplicate records

```sql
SELECT
	id,
	COUNT(*) as cnt
FROM product_emissions
GROUP BY id
HAVING COUNT(*) > 1;
```
