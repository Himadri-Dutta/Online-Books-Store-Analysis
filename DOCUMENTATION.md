# Online Bookstore SQL – Documentation

## 1. Purpose

This project builds a small relational database for an online bookstore and uses SQL to answer common business questions about inventory, customers, and sales.

## 2. Tables

### Books
| Column | Type | Notes |
|--------|------|-------|
| Book_ID | SERIAL | Primary key |
| Title | VARCHAR(100) | Book title |
| Author | VARCHAR(100) | Author name |
| Genre | VARCHAR(50) | e.g. Fiction, Fantasy |
| Published_Year | INT | Year of publication |
| Price | NUMERIC(10,2) | Unit price |
| Stock | INT | Copies available |

### Customers
| Column | Type | Notes |
|--------|------|-------|
| Customer_ID | SERIAL | Primary key |
| Name | VARCHAR(100) | Full name |
| Email | VARCHAR(100) | Contact email |
| Phone | VARCHAR(15) | Contact phone |
| City | VARCHAR(50) | City |
| Country | VARCHAR(150) | Country |

### Orders
| Column | Type | Notes |
|--------|------|-------|
| Order_ID | SERIAL | Primary key |
| Customer_ID | INT | FK → Customers(Customer_ID) |
| Book_ID | INT | FK → Books(Book_ID) |
| Order_Date | DATE | Date of order |
| Quantity | INT | Units ordered |
| Total_Amount | NUMERIC(10,2) | Order value |

**Relationships:** one customer can place many orders; one book can appear in many orders. Each order row links one customer to one book.

## 3. Data Import

Data is loaded from CSV files with `LOAD DATA INFILE`, skipping the header row (`IGNORE 1 ROWS`). Load parent tables (`Books`, `Customers`) before `Orders` so foreign keys are satisfied.

### Known issues in the current script

The import section has copy-paste mistakes that will load data into the wrong table:

| Comment says | File used | Table targeted | Should be |
|--------------|-----------|----------------|-----------|
| Books Table | `Orders.csv` | `Orders` | `Books.csv` → `Books` |
| Customers Table | `Books.csv` | `Orders` | `Customers.csv` → `Customers` |
| Orders Table | `Customers.csv` | `Orders` | `Orders.csv` → `Orders` |

Corrected version:

```sql
LOAD DATA INFILE 'C:/path/to/Books.csv'
INTO TABLE Books
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;

LOAD DATA INFILE 'C:/path/to/Customers.csv'
INTO TABLE Customers
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;

LOAD DATA INFILE 'C:/path/to/Orders.csv'
INTO TABLE Orders
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```

Other things to watch:
- Windows CSVs often use `\r\n` line endings; if rows import with stray characters, use `LINES TERMINATED BY '\r\n'`.
- `DROP TABLE IF EXISTS customers;` and `CREATE TABLE Customers` differ in case. This is fine on Windows but may fail on case-sensitive Linux MySQL setups. Keep names consistent.
- The tables are dropped in creation order. When re-running, drop `Orders` first because it depends on the other two.

## 4. Query Reference

### Basic queries

| # | Question | Technique |
|---|----------|-----------|
| 1 | Books in the Fiction genre | `WHERE` |
| 2 | Books published after 1950 | `WHERE` with comparison |
| 3 | Customers from Canada | `WHERE` |
| 4 | Orders placed in November 2023 | `BETWEEN` on dates |
| 5 | Total stock available | `SUM` |
| 6 | Most expensive book | `ORDER BY ... DESC LIMIT 1` |
| 7 | Orders with quantity > 1 | `WHERE` |
| 8 | Orders with total above $20 | `WHERE` |
| 9 | All available genres | `DISTINCT` |
| 10 | Book with lowest stock | `ORDER BY ... LIMIT 1` |
| 11 | Total revenue | `SUM` |

### Advanced queries

| # | Question | Technique |
|---|----------|-----------|
| 1 | Books sold per genre | `JOIN`, `SUM`, `GROUP BY` |
| 2 | Average price of Fantasy books | `AVG` |
| 3 | Customers with 2+ orders | `JOIN`, `GROUP BY`, `HAVING` |
| 4 | Most frequently ordered book | `JOIN`, `COUNT`, `ORDER BY`, `LIMIT` |
| 5 | Top 3 priciest Fantasy books | `ORDER BY ... LIMIT 3` |
| 6 | Quantity sold per author | `JOIN`, `SUM`, `GROUP BY` |
| 7 | Cities of customers who spent over $30 | `JOIN`, `DISTINCT` |
| 8 | Top-spending customer | `JOIN`, `SUM`, `ORDER BY`, `LIMIT` |
| 9 | Remaining stock after orders | `LEFT JOIN`, `COALESCE`, `SUM` |

## 5. Notes on Specific Queries

- **Basic #4 (November 2023):** `BETWEEN '2023-11-01' AND '2023-11-30'` is correct for a `DATE` column. If the column ever becomes a `DATETIME`, use `< '2023-12-01'` instead so the last day is not cut off.
- **Basic #7:** The comment says "customers", but the query returns order rows. To list customers, join with `Customers` and use `SELECT DISTINCT`.
- **Advanced #3 and #4:** Both count rows per group, so they measure the number of orders, not the quantity of items.
- **Advanced #4:** `LIMIT 1` hides ties. If two books share the top count, only one is shown.
- **Advanced #7:** Selecting `total_amount` alongside `DISTINCT c.city` can return the same city more than once. Remove `total_amount` to get unique cities.
- **Advanced #9:** `GROUP BY b.book_id` alone works in MySQL because `book_id` is the primary key. Stricter engines may require `b.title` and `b.stock` in the `GROUP BY`.
- **Stray statement:** `SELECT * FROM ORDERS;` appears before Advanced #1 and can be removed.

## 6. Suggested Improvements

- Use one SQL dialect consistently (MySQL or PostgreSQL).
- Add indexes on `Orders(Customer_ID)`, `Orders(Book_ID)`, and `Books(Genre)` for faster joins and filters.
- Add constraints such as `NOT NULL`, `CHECK (Quantity > 0)`, and `UNIQUE (Email)`.
- Consider extra analysis: monthly revenue trends, revenue by country, and best-selling authors per genre.
