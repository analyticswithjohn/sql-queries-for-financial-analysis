# Basic SQL Queries Examples

This document provides examples of basic SQL queries using the Sample Sales Data dataset. These examples cover fundamental SQL operations including SELECT statements, filtering with WHERE, sorting with ORDER BY, and limiting results.

## SELECT Statements

### Example 1: Retrieving All Columns

Retrieve all columns from the sales_data table:

```sql
SELECT * FROM sales_data;
```

**Use Case**: When you need to explore the entire dataset to understand its structure and contents.

**Edge Case**: This query can be resource-intensive for large tables. Avoid using `SELECT *` in production code or for large datasets.

### Example 2: Selecting Specific Columns

Retrieve only the order information:

```sql
SELECT 
    ORDERNUMBER, 
    ORDERDATE, 
    STATUS, 
    CUSTOMERNAME
FROM sales_data;
```

**Use Case**: When you only need specific columns, which is more efficient than retrieving all columns.

**Best Practice**: Always specify only the columns you need rather than using `SELECT *`.

### Example 3: Using Column Aliases

Retrieve sales data with more readable column names:

```sql
SELECT 
    ORDERNUMBER AS "Order Number",
    ORDERDATE AS "Order Date",
    CUSTOMERNAME AS "Customer Name",
    SALES AS "Sale Amount"
FROM sales_data;
```

**Use Case**: Improving readability of query results, especially for reports or user interfaces.

**Edge Case**: Aliases with spaces require quotes in most database systems.

### Example 4: Performing Calculations

Calculate the total value of each order line:

```sql
SELECT 
    ORDERNUMBER,
    PRODUCTCODE,
    QUANTITYORDERED,
    PRICEEACH,
    QUANTITYORDERED * PRICEEACH AS "Line Total"
FROM sales_data;
```

**Use Case**: Deriving new values from existing columns.

**Edge Case**: Calculations with NULL values typically result in NULL. Use COALESCE or IFNULL functions to handle NULL values if needed.

### Example 5: Distinct Values

Retrieve unique product lines:

```sql
SELECT DISTINCT PRODUCTLINE
FROM sales_data;
```

**Use Case**: Finding all unique categories or values in a column without duplicates.

**Edge Case**: DISTINCT operates on the entire row when multiple columns are specified.

## Filtering with WHERE

### Example 1: Simple Equality Condition

Find all orders with "Shipped" status:

```sql
SELECT 
    ORDERNUMBER, 
    ORDERDATE, 
    CUSTOMERNAME, 
    SALES
FROM sales_data
WHERE STATUS = 'Shipped';
```

**Use Case**: Filtering records based on a specific value.

**Edge Case**: String comparisons are case-sensitive in many database systems.

### Example 2: Multiple Conditions with AND/OR

Find all shipped orders from USA customers in 2005:

```sql
SELECT 
    ORDERNUMBER, 
    ORDERDATE, 
    CUSTOMERNAME, 
    COUNTRY, 
    SALES
FROM sales_data
WHERE STATUS = 'Shipped' 
  AND COUNTRY = 'USA' 
  AND YEAR_ID = 2005;
```

**Use Case**: Filtering records based on multiple criteria that all must be true.

**Edge Case**: Be careful with operator precedence; AND has higher precedence than OR.

### Example 3: Range Conditions with BETWEEN

Find orders with sales between $5,000 and $10,000:

```sql
SELECT 
    ORDERNUMBER, 
    ORDERDATE, 
    CUSTOMERNAME, 
    SALES
FROM sales_data
WHERE SALES BETWEEN 5000 AND 10000
ORDER BY SALES DESC;
```

**Use Case**: Filtering numeric or date values within a specific range.

**Edge Case**: BETWEEN is inclusive of the boundary values.

### Example 4: List Conditions with IN

Find orders from specific countries:

```sql
SELECT 
    ORDERNUMBER, 
    CUSTOMERNAME, 
    COUNTRY, 
    SALES
FROM sales_data
WHERE COUNTRY IN ('USA', 'France', 'UK');
```

**Use Case**: Filtering based on a list of possible values.

**Edge Case**: IN can be more efficient than multiple OR conditions for long lists.

### Example 5: Pattern Matching with LIKE

Find customers with names starting with 'Mini':

```sql
SELECT 
    CUSTOMERNAME, 
    CONTACTFIRSTNAME, 
    CONTACTLASTNAME, 
    COUNTRY
FROM sales_data
WHERE CUSTOMERNAME LIKE 'Mini%';
```

**Use Case**: Searching for text patterns.

**Edge Case**: Performance can degrade with leading wildcards (e.g., '%Mini').

### Example 6: NULL Handling

Find orders without a second address line:

```sql
SELECT 
    ORDERNUMBER, 
    CUSTOMERNAME, 
    ADDRESSLINE1, 
    ADDRESSLINE2
FROM sales_data
WHERE ADDRESSLINE2 IS NULL;
```

**Use Case**: Identifying missing data.

**Edge Case**: NULL cannot be compared with = or !=; must use IS NULL or IS NOT NULL.

## Sorting with ORDER BY

### Example 1: Simple Sorting

Sort orders by date (newest first):

```sql
SELECT 
    ORDERNUMBER, 
    ORDERDATE, 
    CUSTOMERNAME, 
    SALES
FROM sales_data
ORDER BY ORDERDATE DESC;
```

**Use Case**: Displaying data in a specific sequence.

**Edge Case**: NULL values are typically sorted first or last depending on the database system.

### Example 2: Multi-Column Sorting

Sort orders by country, then by sales amount (highest to lowest):

```sql
SELECT 
    ORDERNUMBER, 
    COUNTRY, 
    CUSTOMERNAME, 
    SALES
FROM sales_data
ORDER BY COUNTRY ASC, SALES DESC;
```

**Use Case**: Hierarchical sorting where primary grouping is by one column and secondary sorting by another.

**Edge Case**: Each column can have its own sort direction (ASC or DESC).

### Example 3: Sorting by Calculated Values

Sort orders by profit margin (calculated):

```sql
SELECT 
    ORDERNUMBER, 
    PRODUCTCODE, 
    SALES, 
    QUANTITYORDERED, 
    PRICEEACH, 
    (PRICEEACH - MSRP) / MSRP * 100 AS "Margin Percentage"
FROM sales_data
ORDER BY ((PRICEEACH - MSRP) / MSRP * 100) DESC;
```

**Use Case**: Sorting by derived values that aren't stored in the table.

**Edge Case**: You can sort by the calculation directly or by the alias, depending on the database system.

### Example 4: Sorting by Column Position

Sort by the second column in the result set:

```sql
SELECT 
    ORDERNUMBER, 
    ORDERDATE, 
    SALES
FROM sales_data
ORDER BY 2 DESC;
```

**Use Case**: Quick sorting without typing the column name.

**Edge Case**: This approach is less maintainable as changes to the SELECT list will change the sort column.

## Limiting Results

### Example 1: Top N Records

Retrieve the top 10 highest value orders:

```sql
-- SQL Server
SELECT TOP 10 
    ORDERNUMBER, 
    CUSTOMERNAME, 
    SALES
FROM sales_data
ORDER BY SALES DESC;

-- MySQL, PostgreSQL, SQLite
SELECT 
    ORDERNUMBER, 
    CUSTOMERNAME, 
    SALES
FROM sales_data
ORDER BY SALES DESC
LIMIT 10;
```

**Use Case**: Finding the highest or lowest values in a dataset.

**Edge Case**: Syntax varies by database system.

### Example 2: Pagination

Implement pagination to display orders 11-20:

```sql
-- SQL Server
SELECT 
    ORDERNUMBER, 
    ORDERDATE, 
    CUSTOMERNAME, 
    SALES
FROM sales_data
ORDER BY ORDERNUMBER
OFFSET 10 ROWS
FETCH NEXT 10 ROWS ONLY;

-- MySQL, SQLite
SELECT 
    ORDERNUMBER, 
    ORDERDATE, 
    CUSTOMERNAME, 
    SALES
FROM sales_data
ORDER BY ORDERNUMBER
LIMIT 10 OFFSET 10;
```

**Use Case**: Implementing pagination in applications.

**Edge Case**: Always use ORDER BY with LIMIT/OFFSET to ensure consistent results.

### Example 3: Random Sampling

Select 5 random orders:

```sql
-- PostgreSQL
SELECT 
    ORDERNUMBER, 
    CUSTOMERNAME, 
    SALES
FROM sales_data
ORDER BY RANDOM()
LIMIT 5;

-- SQL Server
SELECT TOP 5
    ORDERNUMBER, 
    CUSTOMERNAME, 
    SALES
FROM sales_data
ORDER BY NEWID();
```

**Use Case**: Statistical sampling or displaying random featured items.

**Edge Case**: Performance can be poor for large tables; consider alternative sampling methods for production use.

## Combining Techniques

### Example 1: Filtered, Calculated, and Sorted

Find the top 5 customers by sales volume in 2004:

```sql
SELECT 
    CUSTOMERNAME,
    COUNT(DISTINCT ORDERNUMBER) AS "Number of Orders",
    SUM(SALES) AS "Total Sales"
FROM sales_data
WHERE YEAR_ID = 2004
GROUP BY CUSTOMERNAME
ORDER BY SUM(SALES) DESC
LIMIT 5;
```

**Use Case**: Complex analysis combining multiple SQL techniques.

**Edge Case**: Ensure that all non-aggregated columns in the SELECT list appear in the GROUP BY clause.

### Example 2: Conditional Filtering with Subqueries

Find customers who placed orders larger than the average order size:

```sql
SELECT 
    ORDERNUMBER,
    CUSTOMERNAME,
    SALES
FROM sales_data
WHERE SALES > (
    SELECT AVG(SALES) 
    FROM sales_data
)
ORDER BY SALES DESC;
```

**Use Case**: Filtering based on aggregate calculations.

**Edge Case**: Subqueries can impact performance; consider using CTEs or temporary tables for complex cases.

## Best Practices for Basic Queries

1. **Be Specific**: Always select only the columns you need.
2. **Use Meaningful Aliases**: Make your query results more readable with clear column names.
3. **Add Comments**: Document complex queries, especially for business logic.
4. **Consider Performance**: Be mindful of query performance, especially for large tables.
5. **Use Proper Indentation**: Format your SQL for readability.
6. **Test Edge Cases**: Verify how your query handles NULL values, empty results, etc.
7. **Use Parameters**: For production code, use parameterized queries to prevent SQL injection.
8. **Validate Input**: Always validate and sanitize user input before using it in queries.
