# Pivoting Data in DuckDB

## Introduction
Pivoting data is a common operation in data analysis and reporting, allowing you to transform row-based data into a columnar format. 

DuckDB provides a simplified PIVOT syntax that follows spreadsheet pivot table naming conventions. In this tutorial, we will explore why you might need to pivot data, discuss alternative approaches, and provide examples of how to pivot data using DuckDB's simplified PIVOT syntax.

## Generalized Features of Pivot Queries
When working with pivot queries in DuckDB, you can utilize the following features:

- **Source Data:** Prepare the dataset from which you are pivoting the values. It should contain the necessary columns for the pivot operation, including the pivot column, values to pivot, and any additional columns required for aggregation or grouping.

- **Pivot Column:** Identify the column that will be transposed from rows to columns in the pivot operation. It represents the values that will become the new column headers in the resulting pivot table.

- **Grouping Columns:** Specify the columns to be used for grouping the data. These columns define the rows in the resulting pivot table.

## Dataset
For the examples in this tutorial, we will use the 'orders' dataset.

```sql orders
SELECT * FROM orders;
```

<DataTable data={orders} />

1. **Simple pivot with one pivot column aggregated by count: pivot by year**

```sql
SELECT *
FROM PIVOT orders
ON EXTRACT(YEAR FROM order_datetime)
GROUP BY category;
```

2. **Pivot with sum as the aggregation: pivot by year**

```sql
SELECT *
FROM PIVOT (
  SELECT category,
    EXTRACT(YEAR FROM order_datetime) AS year,
    sales
  FROM orders
) 
ON year;
```

3. **Pivot with average as the aggregation: pivot by year**

```sql
SELECT *
FROM PIVOT (
  SELECT category,
    EXTRACT(YEAR FROM order_datetime) AS year,
    sales
  FROM orders
)
ON year;
```

4. **Complex pivot on two columns: pivot by category and item**

```sql
SELECT *
FROM PIVOT (
  SELECT category,
    item,
    EXTRACT(YEAR FROM order_datetime) AS year,
    sales
  FROM orders
)
ON (category, item);
```

## Conclusion
Pivoting data in DuckDB allows you to transform row-based data into a columnar format using a simplified PIVOT syntax. By understanding the features and examples provided in this tutorial, you can effectively construct pivot queries, manipulate your data, and gain valuable insights from the resulting pivot tables.