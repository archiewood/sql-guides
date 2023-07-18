# Pivoting Data in PostgreSQL

## Introduction
Pivoting data is a common operation in data analysis and reporting, allowing you to transform row-based data into a columnar format. 

PostgreSQL provides various techniques to achieve pivoting efficiently. In this tutorial, we will explore why you might need to pivot data, discuss alternative approaches, and provide examples of how to pivot data in different scenarios using PostgreSQL.

## Generalized Features of Pivot Queries
When working with pivot queries, there are several key aspects to consider:

1. **Source Data:** Prepare the dataset from which you are pivoting the values. It should contain the necessary columns for the pivot operation, including the pivot field, values to pivot, and any additional columns required for aggregation or grouping.

2. **Aggregation Function:** Specify the aggregation function to be applied to the values being pivoted. Common aggregation functions include SUM, COUNT, AVG, MAX, MIN, etc.

3. **Pivot Field:** Identify the column that will be transposed from rows to columns in the pivot operation. It represents the values that will become the new column headers in the resulting pivot table.

4. **Values to Pivot:** Provide the distinct values from the pivot field that will be transposed into separate columns in the pivot table. These values can be specified explicitly or dynamically generated based on your dataset.

## Dataset
For the examples in this tutorial, we will use the 'orders' dataset.

```sql orders
SELECT * FROM orders;
```

<DataTable data={orders} />

### Pivot with count as the aggregation: pivot by year

```sql
SELECT * 
FROM (
    SELECT category,
        EXTRACT(YEAR FROM order_datetime) AS year,
        COUNT(*) AS total_orders
    FROM orders
    GROUP BY category, year
)
FROM orders
GROUP BY category;
```

### Pivot with sum as the aggregation: pivot by year

```sql
SELECT *
FROM (
  SELECT category,
    EXTRACT(YEAR FROM order_datetime) AS year,
    SUM(sales) AS total_sales
  FROM orders
  GROUP BY category, year
) AS subquery
PIVOT (
  SUM(total_sales) FOR year IN ('2019', '2020', '2021')
) AS pivoted_table;
```

### Pivot with average as the aggregation: pivot by year

```sql
SELECT *
FROM (
  SELECT category,
    EXTRACT(YEAR FROM order_datetime) AS year,
    AVG(sales) AS average_sales
  FROM orders
  GROUP BY category, year
) AS subquery
PIVOT (
  AVG(average_sales) FOR year IN ('2019', '2020', '2021')
) AS pivoted_table;
```

### Complex pivot on two fields: pivot by category and item

```sql
SELECT *
FROM (
  SELECT category,
    item,
    EXTRACT(YEAR FROM order_datetime) AS year,
    SUM(sales) AS total_sales
  FROM orders
  GROUP BY category, item, year
) AS subquery
PIVOT (
  SUM(total_sales) FOR (year) IN (
    ('2019'), ('2020'), ('2021')
  )
) AS pivoted_table;
```

## Conclusion
Pivoting data in PostgreSQL allows you to transform row-based data into a columnar format, enabling better analysis and reporting. By understanding the generalized features and examples provided in this tutorial, you can effectively construct pivot queries, manipulate your data, and gain valuable insights from the resulting pivot tables.