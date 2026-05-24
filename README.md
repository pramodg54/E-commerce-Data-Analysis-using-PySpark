# E-commerce-Data-Analysis-using-PySpark

## Project Overview

This project analyzes e-commerce transactional data using PySpark DataFrames.  
The objective is to load, clean, transform, join, and analyze multiple datasets to generate business insights related to products, customers, orders, brands, categories, and regions.

The analysis is performed using Spark DataFrames in a Jupyter Notebook.

---

## Dataset Files

The project uses four CSV files:

- `users.csv`
- `orders.csv`
- `order_items.csv`
- `products.csv`

These files are joined together to create a final analysis-ready DataFrame.

---

## Tools and Technologies

- Python
- PySpark
- Spark DataFrames
- Jupyter Notebook
- Pandas
- Parquet

---

## Project Objectives

- Load CSV files into Spark DataFrames
- Inspect schema, sample records, and row counts
- Identify and handle missing values
- Clean join keys and descriptive fields
- Convert price-related columns to numeric format
- Perform transformations and filtering
- Join multiple datasets into a final DataFrame
- Generate business insights using aggregations
- Apply Spark optimization concepts such as cache, broadcast join, and repartitioning
- Save output in Parquet format

---

## Data Cleaning

The following cleaning steps were performed:

- Checked missing values in all datasets
- Removed rows with null join keys
- Replaced missing product fields such as `brand`, `name`, and `category` with `"unknown"`
- Converted `sale_price`, `retail_price`, and `cost` columns to numeric format

Missing values in columns such as `returned_at`, `delivered_at`, and `shipped_at` were not removed because they represent order lifecycle events.

---

## Transformations

The following transformations were applied:

- Selected required columns from `order_items`
- Created a new column called `price_bucket`
  - Low: sale price below 25
  - Medium: sale price between 25 and 75
  - High: sale price above 75
- Filtered records where `sale_price > 20`
- Calculated total rows, unique orders, unique users, and unique products

---

## Joins

The datasets were joined using the following keys:

| Dataset 1 | Key | Dataset 2 | Key |
|---|---|---|---|
| users | id | orders | user_id |
| orders | order_id | order_items | order_id |
| order_items | product_id | products | id |

A final DataFrame named `final_df` was created for analysis.

---

## Business Insights

The analysis answered the following questions:

- Top 10 product categories by items sold
- Top 10 brands by total sales
- Top 10 states by number of orders
- Top 10 states by total sales value
- Average sale price by category
- Count of completed, returned, and cancelled items
- State with highest sales
- Category with highest revenue
- Most frequently appearing brand
- Gender-wise order count
- Orders containing more than one item
- Categories with highest average sale price

---

## Key Findings

- Guangdong generated the highest total sales.
- Outerwear & Coats generated the highest revenue.
- Calvin Klein appeared most frequently.
- Male customers placed more orders than female customers.
- Outerwear & Coats had the highest average sale price.
- Several orders contained more than one item.

---

## Spark Optimization

### Cache / Persist

`final_df` was cached because it was reused for multiple analyses.  
Caching helps Spark avoid recomputing joins repeatedly and improves performance for repeated aggregations.

### Broadcast Join

A broadcast join was used while joining the smaller `products` DataFrame with transactional data.  
Broadcast joins reduce shuffle by sending the smaller table to all worker nodes.

### Repartitioning

The final DataFrame was repartitioned by `state`.  
This is useful because state is an important business dimension for regional sales and order analysis.

---

## Bonus Analysis

Additional analysis was performed to compare price-related measures across categories:

- Average sale price
- Average retail price
- Average cost
- Average margin

This helps identify high-value product categories and categories with stronger margins.

---

## Output

The final DataFrame was repartitioned by state and written in Parquet format.

```python
final_df_repartitioned = final_df.repartition("state")

final_df_repartitioned.write.mode("overwrite").parquet(
    "output/final_df_by_state_parquet"
)
