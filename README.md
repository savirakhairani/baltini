# BALTINI
A Python project using SCD Type 2 for identifying and merging duplicate products in the Baltini database. Features include automated duplicate detection, efficient SQL queries for 350k+ records, and database optimizations. Built with Pandas and SQLAlchemy to streamline data processing and improve sales performance.

## Problem Statement
The database contains around 350k product entries, many of which are duplicates sourced from different suppliers. Duplicate entries can confuse customers and distort sales metrics. The goal is to detect and merge duplicate products into a single unique product based on shared indicators such as:

- Product ID (available in product tags)
- Gender
- Product Category

## Objective
1. Generate product merge suggestions daily and insert them into two tables:
- product_duplicates
- product_duplicate_details
2. Provide a SQL query to retrieve merge suggestions in a report format.

## Features

1. Daily Automation
- The script automates the process of detecting and updating duplicate product entries in the database, running every day.
2. Normalized Data Processing
- Product titles are normalized (lowercase and spaces replaced) to ensure case-insensitivity and uniformity.
- Duplicate products are identified using a combination of product ID (inside product tags) gender, and category.
3. Efficient Updates
- New duplicate entries are inserted into the product_duplicates table.
- Existing duplicates are updated with the latest data if necessary.
4. Report Generation
A SQL query is provided to retrieve a summarized report of duplicate groups, including:
- Group Title
- Number of Products
- Last Updated Date

## Prerequisites

1. Database
- MySQL database named baltini must be set up.
- Tables used: products, product_duplicates, and product_duplicate_lists.
2. Python Environment
- Python 3.8+
- Required libraries: **pandas**, **sqlalchemy**,**pymysql**
```
pip install pandas sqlalchemy pymysql
```
3. Connection Configuration
Update the database connection details in the connect_baltini() function:
```db_config = {
    'host': 'localhost',
    'port': '3307',
    'user': 'root',
    'password': 'root',
    'database': 'baltini'
}
```

## Assumption 
1. Product Duplicate Detection:
- The product `title` in the `product_duplicates` table is assumed to be a concatenation of the product ID, gender, and category from product tags. This concatenated value serves as the key to identify similar products.
- The logic assumes that duplicates arise when there are multiple products with the same concatenated key (product ID, gender, category).
2. Partition Strategy
/n For large datasets, partitioning can be helpful in improving query performance. In this case:
- Monthly partitioning is recommended since total daily data is around 350k then it's best to improve query performance by monthly.
3. Indexing Strategy:
- Functional indexing on the concatenated expression (e.g., `CONCAT(SUBSTRING_INDEX(SUBSTRING_INDEX(tags, 'ProductID: ', -1), ',', 1), '-', LOWER(gender), '-', LOWER(category)))` is assumed to speed up queries that involve this expression.
- The `CONCAT` function uses `SUBSTRING_INDEX` and `LOWER` functions, so creating an index directly on this expression can optimize query performance during lookups, filtering, and joining.
- Indexing key columns such as `created_at`, `updated_at`, and `product_id` in the `products` table is also assumed to improve query speed.

These assumptions are based on the given problem statement and are considered optimal for managing product duplicates, partitioning the database, and improving query performance.
