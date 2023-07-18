# Pivoting Data in Snowflake SQL

## Introduction
Pivoting data is a common operation in data analysis and reporting, allowing you to transform row-based data into a columnar format. 

Snowflake SQL provides a powerful PIVOT function to perform this transformation efficiently. In this tutorial, we will explore why you might need to pivot data, discuss alternative approaches, and provide examples of how to pivot data in different scenarios using Snowflake SQL.

## Generalized Features of Pivot Queries
When working with pivot queries, there are several key aspects to consider:

1.. **Source Expression:** Create the dataset from which you are pivoting the values. It should contain only the columns that will either show up as pivot fields, row fields, or aggregation functions in the final pivot table. 
1. **Aggregation Function:** Specify the aggregation function to be applied to the values being pivoted. Common aggregation functions include SUM, COUNT, AVG, MAX, MIN, etc.
1. **Pivot Field:** Identify the column that will be transposed from rows to columns in the pivot operation. It represents the values that will become the new column headers in the resulting pivot table.
1. **Values to Pivot:** Provide the distinct values from the pivot field that will be transposed into separate columns in the pivot table. These values can be specified explicitly or dynamically generated based on your dataset.

## Dataset
For the examples in this tutorial, we will use the neeful things 'orders' dataset.

```sql orders
select * from orders;
```

<DataTable data={orders} />

### Simple pivot with one pivot field aggregated by count: pivot by year

```sql
SELECT category, "2019", "2020", "2021" 
FROM ( -- Source expression
  SELECT 
    category,
    EXTRACT(YEAR FROM order_datetime) AS year, -- pivot field (year)
    COUNT(*) AS number_of_orders -- Aggregation function (COUNT) 
  FROM orders 
  GROUP BY category, year 
)
PIVOT (
  SUM(number_of_orders) FOR year IN (2019, 2020, 2021) -- Values to pivot (year)
)

```

### Pivot with sum as the aggregation: pivot by year

```sql
SELECT *
FROM ( -- Source expression
  SELECT 
    category,  
    EXTRACT(YEAR FROM order_datetime) AS year, -- Pivot field (year)
    sales 
  FROM orders -- Source data
)
PIVOT (SUM(sales) FOR year IN (2019, 2020, 2021)) -- Aggregation function (SUM) and values to pivot (year)
```

### Pivot with average as the aggregation: pivot by year

```sql
SELECT *
FROM ( -- Source expression
  SELECT 
    category,
    EXTRACT(YEAR FROM order_datetime) AS year,  -- Pivot field (year)
    sales 
  FROM orders -- Source data
)
PIVOT (AVG(sales) FOR year IN (2019, 2020, 2021)) -- Aggregation function (AVG) and values to pivot (year)
```

### Complex pivot on two fields: pivot by category and item
```sql
SELECT *
FROM ( -- Source expression
  SELECT 
    category, -- Pivot field (category)
    item, -- Pivot field (item)
    EXTRACT(YEAR FROM order_datetime) AS year,
    sales
  FROM orders 
)
PIVOT (SUM(sales) FOR (category , item)  -- Aggregation function (SUM)
  IN (
    ('Sinister Toys', 'Model Racehorse'), 
    ('Sinister Toys', 'Baseball Card'), 
    ('Mysterious Apparel', 'Mystic Pendant'), 
    ('Mysterious Apparel', 'Vintage Jacket'), 
    ('Cursed Sporting Goods', 'Fishing Rod'), 
    ('Cursed Sporting Goods', 'Running Shoes'), 
    ('Cursed Sporting Goods', 'Boxing Gloves'), 
    ('Odd Equipment', 'Lamp'), 
    ('Odd Equipment', 'Necklace'),
    ('Odd Equipment', 'Microscope')
    )) -- Values to pivot (category, item)
```



## Conclusion
Pivoting data in Snowflake SQL allows you to transform row-based data into a columnar format, enabling better analysis and reporting. 

By understanding the generalized features and examples provided in this tutorial, you can effectively construct pivot queries, manipulate your data, and gain valuable insights from the resulting pivot tables.