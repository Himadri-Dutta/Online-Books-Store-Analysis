# 📚 Online Bookstore – SQL Analysis Project

A beginner-to-intermediate SQL project that models an online bookstore with three related tables (**Books**, **Customers**, **Orders**) and answers 20 business questions using filtering, aggregation, joins, and grouping.

## Project Overview

| Item | Details |
|------|---------|
| Database | `OnlineBookstore` |
| Tables | `Books`, `Customers`, `Orders` |
| Data source | CSV files (`Books.csv`, `Customers.csv`, `Orders.csv`) |
| SQL dialect | MySQL (see [Compatibility notes](#compatibility-notes)) |
| Queries | 11 basic + 9 advanced |

## Repository Structure

```
.
├── OnlineBookstore.sql      # Schema, data import, and all queries
├── Books.csv                # Book catalogue data
├── Customers.csv            # Customer data
├── Orders.csv               # Order transactions
├── README.md                # This file
└── DOCUMENTATION.md         # Schema and query reference
```

## Database Schema

```
Customers (1) ────< Orders >──── (1) Books
```

- **Books**: `Book_ID`, `Title`, `Author`, `Genre`, `Published_Year`, `Price`, `Stock`
- **Customers**: `Customer_ID`, `Name`, `Email`, `Phone`, `City`, `Country`
- **Orders**: `Order_ID`, `Customer_ID` (FK), `Book_ID` (FK), `Order_Date`, `Quantity`, `Total_Amount`

## Getting Started

1. **Create the database and tables** by running the first section of the SQL file.
2. **Place the CSV files** in a folder your MySQL server can read, and update the file paths in the `LOAD DATA INFILE` statements.
3. **Load the data** in this order (parents before children): `Books` → `Customers` → `Orders`.
4. **Run the queries** section by section and inspect the results.

> Tip: If MySQL blocks the import, check `SHOW VARIABLES LIKE 'secure_file_priv';` and move the CSVs into the folder it returns, or use the Table Data Import Wizard in MySQL Workbench.

## Questions Answered

**Basic**
1. All books in the Fiction genre
2. Books published after 1950
3. Customers from Canada
4. Orders placed in November 2023
5. Total stock of all books
6. Most expensive book
7. Orders with quantity greater than 1
8. Orders with total amount above $20
9. All distinct genres
10. Book with the lowest stock
11. Total revenue from all orders

**Advanced**
1. Total books sold per genre
2. Average price of Fantasy books
3. Customers with at least 2 orders
4. Most frequently ordered book
5. Top 3 most expensive Fantasy books
6. Total quantity sold per author
7. Cities of customers who spent over $30
8. Customer who spent the most
9. Remaining stock after fulfilling all orders

## SQL Concepts Used

`SELECT` · `WHERE` · `BETWEEN` · `ORDER BY` · `LIMIT` · `DISTINCT` · `SUM` / `AVG` / `COUNT` · `GROUP BY` · `HAVING` · `INNER JOIN` · `LEFT JOIN` · `COALESCE` · Primary & Foreign Keys

## Compatibility Notes

The script mixes MySQL and PostgreSQL syntax. Before running, be aware of the following:

- `CREATE DATABASE IF NOT EXISTS`, `USE`, and `LOAD DATA INFILE` are **MySQL** features.
- `SERIAL` works in both, but in MySQL it creates an unsigned `BIGINT`, which can clash with the `INT` foreign key columns. Using `INT AUTO_INCREMENT PRIMARY KEY` is safer for MySQL.
- Inline `REFERENCES` is ignored by MySQL. Use `FOREIGN KEY (...) REFERENCES ...` instead.
- In **PostgreSQL**, use `COPY ... FROM ... CSV HEADER` (or `\copy`) for imports.

See `DOCUMENTATION.md` for the known issues in the import section.

## Author

Created as a SQL practice project. Feel free to fork, extend, and adapt it.
