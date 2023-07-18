# Pivoting Data in BigQuery

## Introduction
Pivoting data is a common operation in data analysis and reporting, allowing you to transform row-based data into a columnar format. 

BigQuery provides flexible features and functions to perform pivoting efficiently. In this tutorial, we will explore why you might need to pivot data, discuss alternative approaches, and provide examples of how to pivot data in different scenarios using BigQuery.

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

1. **Simple pivot with one pivot field aggregated by count: pivot by year**

```sql
SELECT category, 
    COUNTIF(EXTRACT(YEAR FROM order_datetime) = 2019) AS "2019",
    COUNTIF(EXTRACT(YEAR FROM order_datetime) = 2020) AS "2020",
    COUNTIF(EXTRACT(YEAR FROM order_datetime) = 2021) AS "2021"
FROM `project.dataset.orders`
GROUP BY category;
```

2. **Pivot with sum as the aggregation: pivot by year**

```sql
SELECT *
FROM (
  SELECT category,
    EXTRACT(YEAR FROM order_datetime) AS year,
    SUM(sales) AS total_sales
  FROM `project.dataset.orders`
  GROUP BY category, year
)
PIVOT (
  SUM(total_sales) FOR year IN (2019, 2020, 2021)
);
```

3. **Pivot with average as the aggregation: pivot by year**

```sql
SELECT *
FROM (
  SELECT category,
    EXTRACT(YEAR FROM order_datetime) AS year,
    AVG(sales) AS average_sales
  FROM `project.dataset.orders`
  GROUP BY category, year
)
PIVOT (
  AVG(average_sales) FOR year IN (2019, 2020, 2021)
);
```

4. **Complex pivot on two fields: pivot by category and item**

```sql
SELECT *
FROM (
  SELECT category,
    item,
    EXTRACT(YEAR FROM order_datetime) AS year,
    SUM(sales) AS total_sales
  FROM `project.dataset.orders`
  GROUP BY category, item, year
)
PIVOT (
  SUM(total_sales) FOR (year) IN (
    ('2019'), ('2020'), ('2021')
  )
);
```

## Conclusion
Pivoting data in BigQuery allows you to transform row-based data into a columnar format, enabling better analysis and reporting. By understanding the generalized features and examples provided in this tutorial, you can effectively construct pivot queries, manipulate your data, and gain valuable insights from the resulting pivot tables.