# SQL

SQL (Structured Query Language) is the backbone of data management in relational databases. It's not just a skill; it's a foundational language for anyone interacting with data, be it a backend developer building robust applications, a data scientist extracting insights, a data analyst creating reports, or a system designer architecting scalable data solutions. Mastery of SQL is indispensable for job interviews in these fields and for thriving in real-world database development.
This comprehensive guide will take you from the absolute basics of SQL to its most advanced features, equipping you with the knowledge and practical examples needed for true mastery. We'll cover syntax, best practices, common pitfalls, and touch upon differences across popular RDBMS platforms like MySQL, PostgreSQL, and SQL Server.
What is SQL and its Role?
At its core, SQL is a domain-specific language used to manage data held in a relational database management system (RDBMS) or for stream processing in a relational data stream management system (RDSMS). It's the standard language for:
 * Querying data: Retrieving specific information from a database.
 * Manipulating data: Inserting new records, updating existing ones, and deleting obsolete data.
 * Defining data: Creating and modifying the structure of databases and tables.
 * Controlling data access: Managing user permissions and security.
Why is SQL Foundational?
 * Backend Development: Web and mobile applications heavily rely on databases to store user data, product information, transactional details, etc. Backend developers use SQL (often via ORMs or direct JDBC/ODBC connections) to interact with these databases.
 * Data Science & Analytics: Data scientists and analysts use SQL to extract, clean, transform, and aggregate data for analysis, machine learning model training, and reporting. It's often the first step in any data-driven project.
 * System Design: Architects and system designers need to understand SQL to design efficient database schemas, optimize queries, and ensure data integrity and scalability for large-scale systems.
🧱 1. Database and Table Fundamentals
What is a database and RDBMS?
A database is an organized collection of structured information, or data, typically stored electronically in a computer system. It's designed for efficient storage, retrieval, and management of data.
A Relational Database Management System (RDBMS) is a software system used to create, manage, and query relational databases. In an RDBMS, data is organized into tables, which consist of rows and columns. These tables are related to each other based on common fields.
Examples of RDBMS: MySQL, PostgreSQL, Oracle, SQL Server, SQLite.
Creating, Dropping, and Using Databases
CREATE DATABASE
Used to create a new database.
Syntax:
CREATE DATABASE database_name;

Example:
CREATE DATABASE bookstore_db;

DROP DATABASE
Used to delete an existing database. Use with extreme caution! This operation is irreversible.
Syntax:
DROP DATABASE database_name;

Example:
DROP DATABASE old_project_db;

USE
Used to select a database to work with. Once selected, subsequent SQL commands will operate within that database until another USE command is issued or the session ends.
Syntax:
USE database_name;

Example:
USE bookstore_db;

Data Types
Data types define the kind of values a column can hold. Choosing appropriate data types is crucial for data integrity, storage efficiency, and query performance.
 * INT / INTEGER: Whole numbers (e.g., 1, 100, -5).
   * MySQL: INT, TINYINT, SMALLINT, MEDIUMINT, BIGINT (vary by storage size).
   * PostgreSQL: INTEGER, SMALLINT, BIGINT.
   * SQL Server: INT, TINYINT, SMALLINT, BIGINT.
 * VARCHAR(n): Variable-length string, where n is the maximum number of characters (e.g., 'Hello World'). VARCHAR is preferred over CHAR for variable-length text to save space.
 * DECIMAL(p, s) / NUMERIC(p, s): Fixed-point numbers, p is the total number of digits (precision), s is the number of digits after the decimal point (scale) (e.g., 123.45, 9.99). Suitable for monetary values.
 * DATE: Stores a date in 'YYYY-MM-DD' format.
 * TIME: Stores a time in 'HH:MM:SS' format.
 * DATETIME / TIMESTAMP: Stores both date and time. TIMESTAMP often has a smaller range and is commonly used for tracking changes (e.g., last updated). DATETIME typically has a larger range.
   * MySQL: DATETIME, TIMESTAMP.
   * PostgreSQL: TIMESTAMP, TIMESTAMPTZ (with timezone).
   * SQL Server: DATETIME, DATETIME2, SMALLDATETIME.
 * BOOLEAN / TINYINT(1) (MySQL): Stores true/false values. PostgreSQL has a native BOOLEAN type. MySQL often uses TINYINT(1) where 1 represents true and 0 represents false. SQL Server uses BIT.
 * TEXT / BLOB: For large blocks of text (TEXT) or binary data (BLOB for images, files).
 * FLOAT / DOUBLE: Floating-point numbers for approximate decimal values. Less precise than DECIMAL.
Creating, Altering, and Dropping Tables
CREATE TABLE
Used to define a new table in the current database.
Syntax:
CREATE TABLE table_name (
    column1_name data_type [constraints],
    column2_name data_type [constraints],
    ...
    [table_constraints]
);

Example:
USE bookstore_db;

CREATE TABLE Authors (
    author_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    birth_date DATE,
    nationality VARCHAR(50) DEFAULT 'Unknown'
);

CREATE TABLE Books (
    book_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    author_id INT NOT NULL,
    publication_year INT,
    price DECIMAL(10, 2) NOT NULL,
    stock_quantity INT DEFAULT 0,
    FOREIGN KEY (author_id) REFERENCES Authors(author_id)
);

ALTER TABLE
Used to modify the structure of an existing table.
Syntax (Add Column):
ALTER TABLE table_name
ADD COLUMN new_column_name data_type [constraints];

Syntax (Drop Column):
ALTER TABLE table_name
DROP COLUMN column_name;

Syntax (Modify Column):
-- MySQL
ALTER TABLE table_name
MODIFY COLUMN column_name new_data_type [new_constraints];

-- PostgreSQL
ALTER TABLE table_name
ALTER COLUMN column_name TYPE new_data_type;

-- SQL Server
ALTER TABLE table_name
ALTER COLUMN column_name new_data_type;

Example:
ALTER TABLE Books
ADD COLUMN isbn VARCHAR(13) UNIQUE;

ALTER TABLE Authors
DROP COLUMN nationality;

-- MySQL example for modifying column
ALTER TABLE Books
MODIFY COLUMN stock_quantity INT DEFAULT 10;

DROP TABLE
Used to delete an existing table. Use with extreme caution! This deletes the table structure and all its data.
Syntax:
DROP TABLE table_name;

Example:
DROP TABLE old_temp_data;

Setting PRIMARY KEY, AUTO_INCREMENT, and DEFAULT Values
 * PRIMARY KEY: A column (or set of columns) that uniquely identifies each row in a table.
   * Rules: Must contain unique values, cannot contain NULL values.
   * A table can have only one PRIMARY KEY.
   * Automatically creates a clustered index in some RDBMS (e.g., SQL Server) or a B-tree index (e.g., MySQL, PostgreSQL).
 * AUTO_INCREMENT (MySQL) / SERIAL (PostgreSQL) / IDENTITY (SQL Server): Automatically generates a unique number for new records. Useful for primary keys.
   * MySQL: INT PRIMARY KEY AUTO_INCREMENT
   * PostgreSQL: SERIAL or BIGSERIAL (for BIGINT)
   * SQL Server: IDENTITY(seed, increment)
 * DEFAULT value: Specifies a default value for a column when no value is explicitly provided during INSERT.
Example (revisiting CREATE TABLE):
CREATE TABLE Products (
    product_id INT PRIMARY KEY AUTO_INCREMENT, -- MySQL syntax for auto-incrementing PK
    product_name VARCHAR(255) NOT NULL,
    price DECIMAL(10, 2) DEFAULT 0.00,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP -- Sets default to current time
);

-- PostgreSQL equivalent for product_id:
-- product_id SERIAL PRIMARY KEY,

Understanding NULL vs NOT NULL
 * NULL: Represents the absence of a value. It is not equivalent to 0 or an empty string. A column can contain NULL values by default.
 * NOT NULL: A constraint that ensures a column cannot have NULL values. If you try to insert or update a row with a NULL value in a NOT NULL column, the operation will fail.
Practical Scenario:
In an Authors table, first_name and last_name should be NOT NULL because an author must have a name. birth_date might be NULL if the birth date is unknown.
📝 2. Inserting and Managing Data
INSERT INTO (Single & Batch Inserts)
Used to add new rows of data into a table.
Single Row Insert
Syntax:
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...);

-- Or, if inserting values for all columns in order:
INSERT INTO table_name
VALUES (value1, value2, ...);

Example:
USE bookstore_db;

INSERT INTO Authors (first_name, last_name, birth_date)
VALUES ('Stephen', 'King', '1947-09-21');

INSERT INTO Books (title, author_id, publication_year, price)
VALUES ('The Shining', 1, 1977, 15.99); -- Assuming Stephen King's author_id is 1

Batch Inserts (Multiple Rows)
More efficient for inserting many rows at once, as it reduces network round trips.
Syntax:
INSERT INTO table_name (column1, column2, ...)
VALUES
    (value1_row1, value2_row1, ...),
    (value1_row2, value2_row2, ...),
    ...;

Example:
INSERT INTO Authors (first_name, last_name)
VALUES
    ('Agatha', 'Christie'),
    ('J.K.', 'Rowling'),
    ('George', 'Orwell');

INSERT INTO Books (title, author_id, publication_year, price)
VALUES
    ('Murder on the Orient Express', 2, 1934, 12.50),
    ('Harry Potter and the Sorcerer''s Stone', 3, 1997, 18.75),
    ('1984', 4, 1949, 10.00);

SELECT, Column Aliases, and Result Formatting
SELECT
Used to retrieve data from one or more tables.
Syntax:
SELECT column1, column2, ...
FROM table_name;

-- To select all columns:
SELECT *
FROM table_name;

Example:
SELECT title, price
FROM Books;

SELECT *
FROM Authors;

Column Aliases (AS)
Used to give a temporary, more readable name to a column or expression in the result set.
Syntax:
SELECT column_name AS alias_name
FROM table_name;

Example:
SELECT title AS BookTitle, publication_year AS YearPublished
FROM Books;

Result Formatting (Implicit)
SQL results are typically returned as a tabular dataset. Further formatting (e.g., date formats, currency symbols) usually happens in the application layer, though some SQL functions can assist (see String and Date functions later).
Use of Quotes, Escaping Characters
 * Single Quotes ('): Used to enclose string literals (e.g., 'text', '2023-01-01').
 * Backticks (`): (MySQL specific) Used to quote identifiers (database names, table names, column names) if they contain special characters or are reserved keywords.
   * PostgreSQL/SQL Server: Use double quotes (") for identifiers. It's generally good practice to avoid reserved keywords and special characters in names to prevent quoting.
Escaping Characters:
If a single quote is part of a string literal, it needs to be escaped. The standard way is to double the single quote.
Example:
INSERT INTO Books (title, author_id, publication_year, price)
VALUES ('The Hitchhiker''s Guide to the Galaxy', 1, 1979, 9.99); -- 's becomes ''s

Handling Special Characters in MySQL
MySQL, by default, handles most common characters. For specific binary data or very complex character sets, you might need to specify character encodings (e.g., UTF8MB4) at the database or table level. When inserting, generally follow the standard quoting and escaping rules.
Constraints: UNIQUE, CHECK, NOT NULL, Named Constraints
Constraints enforce rules on data in a table, ensuring data integrity.
 * NOT NULL: (Already covered) Ensures a column cannot contain NULL values.
 * UNIQUE: Ensures all values in a column (or set of columns) are unique across all rows. A table can have multiple UNIQUE constraints.
   * Unlike PRIMARY KEY, a UNIQUE column can typically contain NULL values, but only one NULL (as NULL is not considered equal to NULL).
 * CHECK: Defines a rule that values in a column must satisfy.
   * MySQL: CHECK constraints are parsed but not enforced in older versions (before 8.0.16). They are enforced in newer versions.
   * PostgreSQL/SQL Server: Fully enforced.
 * Named Constraints: Giving a name to a constraint makes it easier to manage (e.g., drop or alter) later.
Syntax (with CREATE TABLE):
CREATE TABLE Users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL UNIQUE, -- UNIQUE constraint
    email VARCHAR(100) NOT NULL,
    age INT,
    CONSTRAINT chk_age CHECK (age >= 18), -- Named CHECK constraint
    CONSTRAINT uq_email UNIQUE (email) -- Named UNIQUE constraint
);

Syntax (with ALTER TABLE):
ALTER TABLE Products
ADD CONSTRAINT chk_price CHECK (price > 0);

ALTER TABLE Customers
ADD CONSTRAINT uq_customer_email UNIQUE (email);

🔄 3. CRUD Operations
CRUD stands for Create, Read, Update, Delete – the four basic functions of persistent storage.
Reading Data with SELECT, WHERE, LIKE, Wildcards
SELECT (revisited)
The SELECT statement is the most frequently used SQL command.
Example:
SELECT title, publication_year FROM Books;

WHERE Clause
Used to filter records based on specified conditions. Only rows that satisfy the WHERE condition are returned.
Syntax:
SELECT column1, column2 FROM table_name
WHERE condition;

Example:
SELECT title, price
FROM Books
WHERE price > 15.00;

SELECT *
FROM Authors
WHERE last_name = 'King';

LIKE Operator and Wildcards
Used for pattern matching in string columns.
 * % (percent sign): Represents zero, one, or multiple characters.
 * _ (underscore): Represents a single character.
Syntax:
SELECT column1 FROM table_name
WHERE column_name LIKE pattern;

Example:
-- Find books where the title starts with 'The'
SELECT title FROM Books
WHERE title LIKE 'The%';

-- Find authors whose first name has 'o' as the second letter
SELECT first_name, last_name FROM Authors
WHERE first_name LIKE '_o%';

-- Find titles containing 'Potter'
SELECT title FROM Books
WHERE title LIKE '%Potter%';

Updating Data with UPDATE
Used to modify existing records in a table. Always use a WHERE clause with UPDATE to avoid updating all rows!
Syntax:
UPDATE table_name
SET column1 = new_value1, column2 = new_value2, ...
WHERE condition;

Example:
-- Update the price of 'The Shining'
UPDATE Books
SET price = 16.50
WHERE title = 'The Shining';

-- Update stock quantity for books published before 1990
UPDATE Books
SET stock_quantity = 5
WHERE publication_year < 1990;

Deleting Rows with DELETE
Used to remove existing records from a table. Always use a WHERE clause with DELETE to avoid deleting all rows!
Syntax:
DELETE FROM table_name
WHERE condition;

Example:
-- Delete a specific book
DELETE FROM Books
WHERE title = 'Old Unpopular Book';

-- Delete authors born before 1900
DELETE FROM Authors
WHERE birth_date < '1900-01-01';

TRUNCATE TABLE vs DELETE FROM table_name (without WHERE)
 * DELETE FROM table_name;: Deletes all rows, but logs each row deletion. Can be rolled back (if part of a transaction). Resets AUTO_INCREMENT in some RDBMS (e.g., MySQL), but not universally.
 * TRUNCATE TABLE table_name;: Faster for deleting all rows. It's a DDL (Data Definition Language) command, not DML. It typically deallocates the data pages used by the table, effectively resetting the table. Usually not logged per row, making it faster. Resets AUTO_INCREMENT. Cannot be rolled back.
When to use which:
 * DELETE for selective row deletion or when you need the ability to rollback.
 * TRUNCATE for quickly emptying an entire table when you don't need to rollback.
Using Aliases, Filtering with Comparison Operators
Table Aliases
Used to give a temporary name to a table in a query. Useful in joins (discussed later) or when referring to the same table multiple times.
Syntax:
SELECT T.column1
FROM table_name AS T
WHERE T.column2 = 'value';

Example:
SELECT b.title, b.price
FROM Books AS b
WHERE b.price < 15.00;

Comparison Operators
Used in WHERE clauses to compare values.
 * = : Equal to
 * != or <> : Not equal to
 * > : Greater than
 * < : Less than
 * >= : Greater than or equal to
 * <= : Less than or equal to
Example:
SELECT * FROM Books WHERE publication_year >= 2000;
SELECT * FROM Authors WHERE birth_date < '1950-01-01';

Logical Operators (AND, OR, NOT, IN, BETWEEN, IS NULL)
Used to combine or negate conditions in a WHERE clause.
 * AND: Both conditions must be true.
 * OR: At least one condition must be true.
 * NOT: Negates a condition.
 * IN (value1, value2, ...): Checks if a value matches any value in a list.
 * BETWEEN value1 AND value2: Checks if a value is within a range (inclusive).
 * IS NULL / IS NOT NULL: Checks for NULL values. You cannot use = or != with NULL!
Example:
-- Books published after 2000 AND priced less than 20
SELECT title, price, publication_year
FROM Books
WHERE publication_year > 2000 AND price < 20.00;

-- Authors born in the 19th century OR in the 21st century
SELECT first_name, last_name, birth_date
FROM Authors
WHERE birth_date BETWEEN '1800-01-01' AND '1899-12-31'
   OR birth_date BETWEEN '2000-01-01' AND '2099-12-31';

-- Books by specific author IDs
SELECT title FROM Books
WHERE author_id IN (1, 3);

-- Books with no stock quantity recorded (i.e., NULL)
SELECT title FROM Books
WHERE stock_quantity IS NULL;

-- Books that are NOT published in the 2000s
SELECT title FROM Books
WHERE NOT (publication_year BETWEEN 2000 AND 2009);

Using CASE Expressions for Conditional Logic
The CASE expression allows you to implement IF-THEN-ELSE logic directly within your SQL queries. It's often used in SELECT statements to create new columns based on conditions or in ORDER BY clauses for custom sorting.
Syntax:
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ...
    ELSE resultN
END AS new_column_name

Example:
SELECT
    title,
    price,
    CASE
        WHEN price < 10.00 THEN 'Budget'
        WHEN price >= 10.00 AND price < 20.00 THEN 'Standard'
        ELSE 'Premium'
    END AS PriceCategory
FROM Books;

-- Using CASE in an ORDER BY clause
SELECT title, publication_year
FROM Books
ORDER BY
    CASE
        WHEN publication_year < 1950 THEN 1
        WHEN publication_year >= 1950 AND publication_year < 2000 THEN 2
        ELSE 3
    END,
    title; -- Secondary sort by title within category

🔤 4. String Functions
String functions are essential for manipulating text data within your queries. Syntax can vary slightly between RDBMS.
CONCAT, SUBSTRING, REPLACE, REVERSE
 * CONCAT(string1, string2, ...): Joins two or more strings.
   * MySQL: CONCAT(), or CONCAT_WS() for 'concat with separator'.
   * PostgreSQL: CONCAT(), or || operator.
   * SQL Server: CONCAT(), or + operator.
 * SUBSTRING(string, start, length) / SUBSTR: Extracts a substring from a string. start is 1-based in most SQL dialects.
 * REPLACE(string, old_substring, new_substring): Replaces all occurrences of a specified substring with another substring.
 * REVERSE(string): Reverses the order of characters in a string.
Example:
-- CONCAT (MySQL/PostgreSQL/SQL Server)
SELECT CONCAT(first_name, ' ', last_name) AS FullName
FROM Authors;

-- PostgreSQL specific CONCAT
SELECT first_name || ' ' || last_name AS FullName
FROM Authors;

-- SUBSTRING
SELECT SUBSTRING(title, 1, 10) AS ShortTitle
FROM Books
WHERE LENGTH(title) > 10; -- Using LENGTH to filter

-- REPLACE
SELECT REPLACE(title, 'Harry Potter', 'HP') AS ShortenedTitle
FROM Books
WHERE title LIKE '%Harry Potter%';

-- REVERSE
SELECT REVERSE(title) AS ReversedTitle
FROM Books;

Length Functions (CHAR_LENGTH, LENGTH)
 * LENGTH(string): Returns the length of a string in bytes.
 * CHAR_LENGTH(string) / CHARACTER_LENGTH(string): Returns the length of a string in characters. This is important for multi-byte character sets (like UTF-8) where one character might be multiple bytes.
   * MySQL: Both LENGTH and CHAR_LENGTH are available. LENGTH is bytes, CHAR_LENGTH is characters.
   * PostgreSQL/SQL Server: LENGTH (PostgreSQL) and LEN (SQL Server) typically return character count.
Example:
SELECT title, CHAR_LENGTH(title) AS TitleLengthInChars
FROM Books;

SELECT first_name, LENGTH(first_name) AS NameLengthInBytes
FROM Authors;

SQL Formatting (String Manipulation for Output)
While full formatting often happens in the application layer, SQL can help with some presentation aspects.
 * UPPER(string) / UCASE(string): Converts to uppercase.
 * LOWER(string) / LCASE(string): Converts to lowercase.
 * TRIM(string) / LTRIM(string) / RTRIM(string): Removes leading/trailing spaces.
Example:
SELECT UPPER(title) AS UpperCaseTitle,
       LOWER(first_name) AS LowerCaseFirstName,
       TRIM('   SQL is fun   ') AS TrimmedString
FROM Books LIMIT 1; -- Just to show an example for TRIM

Combining Multiple Functions in One Query
You can nest SQL functions to achieve complex transformations.
Example:
-- Get the first 5 characters of the reversed title, in uppercase
SELECT UPPER(SUBSTRING(REVERSE(title), 1, 5)) AS CustomTitlePart
FROM Books
WHERE title LIKE '%Harry Potter%';

-- Create a username from first initial + last name (lowercase)
SELECT CONCAT(LOWER(SUBSTRING(first_name, 1, 1)), LOWER(last_name)) AS SuggestedUsername
FROM Authors;

📊 5. Aggregate Functions
Aggregate functions perform a calculation on a set of rows and return a single summary value. They are typically used with GROUP BY.
 * COUNT(*) / COUNT(column) / COUNT(DISTINCT column):
   * COUNT(*): Counts all rows, including those with NULL values.
   * COUNT(column): Counts non-NULL values in a specified column.
   * COUNT(DISTINCT column): Counts unique, non-NULL values in a specified column.
 * SUM(column): Calculates the sum of values in a numeric column.
 * AVG(column): Calculates the average of values in a numeric column.
 * MIN(column): Returns the minimum value in a column.
 * MAX(column): Returns the maximum value in a column.
Example:
-- Total number of books
SELECT COUNT(*) AS TotalBooks
FROM Books;

-- Average price of books
SELECT AVG(price) AS AverageBookPrice
FROM Books;

-- Total stock quantity
SELECT SUM(stock_quantity) AS TotalStock
FROM Books;

-- Minimum and maximum publication year
SELECT MIN(publication_year) AS EarliestBook, MAX(publication_year) AS LatestBook
FROM Books;

-- Number of unique publication years
SELECT COUNT(DISTINCT publication_year) AS UniqueYears
FROM Books;

GROUP BY, Multiple Column Grouping
The GROUP BY clause groups rows that have the same values in specified columns into summary rows, allowing aggregate functions to operate on each group.
Syntax:
SELECT column1, aggregate_function(column2)
FROM table_name
GROUP BY column1, column3, ...;

Example:
-- Count books per publication year
SELECT publication_year, COUNT(*) AS NumberOfBooks
FROM Books
GROUP BY publication_year;

-- Calculate average price per author
SELECT a.first_name, a.last_name, AVG(b.price) AS AverageBookPrice
FROM Authors AS a
JOIN Books AS b ON a.author_id = b.author_id
GROUP BY a.author_id, a.first_name, a.last_name; -- Group by author_id and name for uniqueness

Filtering Aggregates with HAVING
The WHERE clause filters individual rows before grouping. The HAVING clause filters groups after they have been formed and aggregated.
Syntax:
SELECT column1, aggregate_function(column2)
FROM table_name
GROUP BY column1
HAVING condition_on_aggregate;

Example:
-- Publication years with more than 2 books
SELECT publication_year, COUNT(*) AS NumberOfBooks
FROM Books
GROUP BY publication_year
HAVING COUNT(*) > 2;

-- Authors whose average book price is above 15.00
SELECT a.first_name, a.last_name, AVG(b.price) AS AverageBookPrice
FROM Authors AS a
JOIN Books AS b ON a.author_id = b.author_id
GROUP BY a.author_id, a.first_name, a.last_name
HAVING AVG(b.price) > 15.00;

Subqueries in Aggregation
Subqueries can be used within aggregate functions, though it's less common to place them directly inside the parentheses of a standard aggregate function. More often, subqueries provide data that is then aggregated in the outer query.
Example (using a subquery to filter before aggregation):
-- Get the average price of books published after 1990
SELECT AVG(price) AS AveragePriceOfRecentBooks
FROM (
    SELECT price
    FROM Books
    WHERE publication_year > 1990
) AS RecentBooks;

This example shows how a subquery can define the dataset for the outer aggregation.
🗂️ 6. Sorting and Filtering
DISTINCT, ORDER BY (ASC/DESC)
 * DISTINCT: Used in the SELECT clause to retrieve only unique values from a column or a set of columns.
   Syntax:
   SELECT DISTINCT column1, column2 FROM table_name;

   Example:
   -- Get all unique publication years
SELECT DISTINCT publication_year FROM Books;

-- Get unique combinations of first and last names from authors
SELECT DISTINCT first_name, last_name FROM Authors;

 * ORDER BY: Used to sort the result set by one or more columns.
   * ASC (Ascending): Default. Sorts from smallest to largest (A-Z, 0-9).
   * DESC (Descending): Sorts from largest to smallest (Z-A, 9-0).
   Syntax:
   SELECT column1, column2 FROM table_name
ORDER BY column_name1 [ASC|DESC], column_name2 [ASC|DESC], ...;

   Example:
   -- Sort books by title alphabetically
SELECT title, publication_year FROM Books
ORDER BY title ASC;

-- Sort books by price descending, then by title ascending for ties
SELECT title, price FROM Books
ORDER BY price DESC, title ASC;

-- Sort authors by birth date, oldest first, and then by last name
SELECT first_name, last_name, birth_date
FROM Authors
ORDER BY birth_date ASC, last_name ASC;

LIMIT for Pagination (LIMIT / OFFSET, TOP, FETCH NEXT)
Used to restrict the number of rows returned by a query. Essential for pagination in applications.
 * MySQL / PostgreSQL: LIMIT and OFFSET
   * LIMIT N: Returns the first N rows.
   * OFFSET M: Skips the first M rows.
   * LIMIT N OFFSET M: Skips M rows and then returns the next N rows.
 * SQL Server: TOP (for returning first N rows) or OFFSET-FETCH (for pagination).
   * SELECT TOP N ... FROM ...
   * OFFSET M ROWS FETCH NEXT N ROWS ONLY
Example (MySQL/PostgreSQL):
-- Get the 5 cheapest books
SELECT title, price FROM Books
ORDER BY price ASC
LIMIT 5;

-- Get the next 5 cheapest books (for pagination, page 2, assuming 5 per page)
SELECT title, price FROM Books
ORDER BY price ASC
LIMIT 5 OFFSET 5;

Example (SQL Server):
-- Get the 5 cheapest books
SELECT TOP 5 title, price FROM Books
ORDER BY price ASC;

-- Get the next 5 cheapest books (for pagination, page 2, assuming 5 per page)
SELECT title, price FROM Books
ORDER BY price ASC
OFFSET 5 ROWS FETCH NEXT 5 ROWS ONLY;

Escaping Wildcards
If you need to search for the literal wildcard characters (%, _) themselves, you must escape them using an ESCAPE clause.
Syntax:
SELECT column_name FROM table_name
WHERE column_name LIKE 'pattern ESCAPE ''escape_character''';

Example:
Suppose you have a product code P_123%ABC.
-- Find products with an underscore directly followed by '123'
SELECT product_code FROM Products
WHERE product_code LIKE 'P\_123%' ESCAPE '\'; -- Using backslash as escape character

-- Find products with a literal '%' character in their name
SELECT product_name FROM Products
WHERE product_name LIKE '%50\%% discount' ESCAPE '\';

The ESCAPE '\' tells SQL that the character immediately following the backslash is to be treated as a literal character, not a wildcard.
LIKE and Pattern Matching (revisited)
As seen before, LIKE is powerful for flexible text searches.
Common scenarios:
 * Starts with: LIKE 'prefix%'
 * Ends with: LIKE '%suffix'
 * Contains: LIKE '%substring%'
 * Specific length: LIKE '___' (three characters)
 * Second character is 'a': LIKE '_a%'
📅 7. Date & Time Operations
Handling dates and times accurately is crucial for many applications.
Data Types: DATE, TIME, DATETIME, TIMESTAMP
(Already covered in Section 1, but reinforcing their importance)
 * DATE: 'YYYY-MM-DD'
 * TIME: 'HH:MM:SS'
 * DATETIME: 'YYYY-MM-DD HH:MM:SS' (fixed range, no timezone info)
 * TIMESTAMP: 'YYYY-MM-DD HH:MM:SS' (often stores UTC, converts to local on retrieval, smaller range, common for audit trails).
Current Date/Time: NOW(), CURDATE(), CURTIME()
These functions retrieve the current date and/or time.
 * MySQL:
   * NOW(): Current date and time (DATETIME).
   * CURDATE(): Current date (DATE).
   * CURTIME(): Current time (TIME).
   * CURRENT_TIMESTAMP(): Same as NOW().
 * PostgreSQL:
   * NOW(): Returns TIMESTAMPTZ (with timezone).
   * CURRENT_DATE: Returns DATE.
   * CURRENT_TIME: Returns TIME (with timezone).
 * SQL Server:
   * GETDATE(): Current date and time (DATETIME).
   * SYSDATETIME(): More precise than GETDATE() (DATETIME2).
   * CURRENT_TIMESTAMP: Same as GETDATE().
Example:
-- MySQL
SELECT NOW() AS CurrentDateTime, CURDATE() AS CurrentDateOnly, CURTIME() AS CurrentTimeOnly;

-- PostgreSQL
SELECT NOW(), CURRENT_DATE, CURRENT_TIME;

-- SQL Server
SELECT GETDATE(), SYSDATETIME();

Date Math, Intervals, Formatting
Performing calculations and formatting dates are common tasks. Syntax varies significantly here.
Date Math (Adding/Subtracting Intervals)
 * MySQL: DATE_ADD(), DATE_SUB(), INTERVAL keyword.
   * DATE_ADD(date, INTERVAL value unit)
   * DATE_SUB(date, INTERVAL value unit)
 * PostgreSQL: INTERVAL keyword with + or - operators.
   * date + INTERVAL 'value unit'
 * SQL Server: DATEADD()
   * DATEADD(unit, value, date)
Units: YEAR, MONTH, DAY, HOUR, MINUTE, SECOND, etc.
Example (Add 7 days to a date):
-- MySQL
SELECT DATE_ADD('2023-01-15', INTERVAL 7 DAY) AS DateAfter7Days;

-- PostgreSQL
SELECT '2023-01-15'::DATE + INTERVAL '7 days' AS DateAfter7Days;

-- SQL Server
SELECT DATEADD(day, 7, '2023-01-15') AS DateAfter7Days;

Example (Difference between dates):
-- MySQL: DATEDIFF(date1, date2) (date1 - date2 in days)
SELECT DATEDIFF('2023-01-20', '2023-01-15') AS DaysDifference;

-- PostgreSQL: date_part('epoch', end_date - start_date) for seconds, or direct subtraction
SELECT '2023-01-20'::DATE - '2023-01-15'::DATE AS DaysDifference;

-- SQL Server: DATEDIFF(unit, start_date, end_date)
SELECT DATEDIFF(day, '2023-01-15', '2023-01-20') AS DaysDifference;

Date Formatting
 * MySQL: DATE_FORMAT(date, format_string)
 * PostgreSQL: TO_CHAR(date, format_string)
 * SQL Server: FORMAT(date, format_string) or CONVERT(data_type, date, style)
Common Format Specifiers (vary by RDBMS):
 * %Y (MySQL/PG) / yyyy (SQL Server): 4-digit year
 * %m (MySQL/PG) / MM (SQL Server): 2-digit month
 * %d (MySQL/PG) / dd (SQL Server): 2-digit day
 * %H (MySQL/PG) / HH (SQL Server): 24-hour format
 * %i (MySQL) / MI (PG) / mm (SQL Server): Minute
 * %s (MySQL) / SS (PG) / ss (SQL Server): Second
Example:
-- MySQL
SELECT DATE_FORMAT(birth_date, '%M %d, %Y') AS FormattedBirthDate
FROM Authors;

-- PostgreSQL
SELECT TO_CHAR(birth_date, 'Month DD, YYYY') AS FormattedBirthDate
FROM Authors;

-- SQL Server
SELECT FORMAT(birth_date, 'MMMM dd, yyyy') AS FormattedBirthDate
FROM Authors;

Using ON UPDATE CURRENT_TIMESTAMP
This is a MySQL-specific feature, often used for TIMESTAMP columns, to automatically update the timestamp of a row whenever it's modified.
Example (during CREATE TABLE):
CREATE TABLE Products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    product_name VARCHAR(255),
    price DECIMAL(10, 2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

When a row in Products is updated, the updated_at column will automatically be set to the current timestamp.
🔄 8. Table Modification and Constraints (revisited and expanded)
ALTER TABLE: Add, Drop, Rename, Modify Columns
We briefly touched on ALTER TABLE in Section 1. Let's expand.
Add Column
ALTER TABLE table_name
ADD COLUMN new_column_name data_type [constraints];

Example:
ALTER TABLE Books
ADD COLUMN genre VARCHAR(100) DEFAULT 'Fiction';

Drop Column
ALTER TABLE table_name
DROP COLUMN column_name;

Example:
ALTER TABLE Authors
DROP COLUMN nationality;

Rename Column
Syntax varies.
 * MySQL:
   ALTER TABLE table_name
RENAME COLUMN old_column_name TO new_column_name;

 * PostgreSQL:
   ALTER TABLE table_name
RENAME COLUMN old_column_name TO new_column_name;

 * SQL Server:
   EXEC sp_rename 'table_name.old_column_name', 'new_column_name', 'COLUMN';

Example (MySQL/PostgreSQL):
ALTER TABLE Books
RENAME COLUMN publication_year TO published_year;

Modify Column (Data Type / Constraints)
Syntax varies slightly.
 * MySQL:
   ALTER TABLE table_name
MODIFY COLUMN column_name new_data_type [new_constraints];

 * PostgreSQL:
   ALTER TABLE table_name
ALTER COLUMN column_name TYPE new_data_type;
ALTER TABLE table_name
ALTER COLUMN column_name SET NOT NULL; -- or DROP NOT NULL
ALTER TABLE table_name
ALTER COLUMN column_name SET DEFAULT default_value; -- or DROP DEFAULT

 * SQL Server:
   ALTER TABLE table_name
ALTER COLUMN column_name new_data_type;

Example:
-- MySQL: Change genre to allow more characters and be NOT NULL
ALTER TABLE Books
MODIFY COLUMN genre VARCHAR(150) NOT NULL DEFAULT 'Fiction';

-- PostgreSQL: Change genre to allow more characters
ALTER TABLE Books
ALTER COLUMN genre TYPE VARCHAR(150);
-- PostgreSQL: Set genre to NOT NULL
ALTER TABLE Books
ALTER COLUMN genre SET NOT NULL;

Adding Constraints After Table Creation
You can add PRIMARY KEY, UNIQUE, CHECK, and FOREIGN KEY constraints after the table has been created.
Primary Key
ALTER TABLE table_name
ADD PRIMARY KEY (column1, column2, ...);

Unique Constraint
ALTER TABLE table_name
ADD CONSTRAINT constraint_name UNIQUE (column1, column2, ...);

Check Constraint
ALTER TABLE table_name
ADD CONSTRAINT constraint_name CHECK (condition);

Foreign Key Constraints and Cascading (ON DELETE CASCADE)
A Foreign Key is a column or set of columns in one table (the "child" table) that refers to the PRIMARY KEY (or a UNIQUE key) in another table (the "parent" table). It establishes a link between two tables, enforcing referential integrity.
Purpose: Ensures that data in the child table has a corresponding entry in the parent table, preventing "orphan" records.
Syntax (during CREATE TABLE):
CREATE TABLE ChildTable (
    child_id INT PRIMARY KEY,
    parent_id INT,
    FOREIGN KEY (parent_id) REFERENCES ParentTable(parent_id)
);

Syntax (with ALTER TABLE):
ALTER TABLE ChildTable
ADD CONSTRAINT fk_parent_id
FOREIGN KEY (parent_id) REFERENCES ParentTable(parent_id);

Cascading Actions
When a row in the parent table is deleted or updated, cascading actions determine what happens to the related rows in the child table.
 * ON DELETE NO ACTION (Default in many RDBMS): If you try to delete a parent row that has matching child rows, the deletion is prevented.
 * ON DELETE RESTRICT: Similar to NO ACTION, deletion is prevented if there are dependent rows.
 * ON DELETE CASCADE: If a row in the parent table is deleted, all corresponding rows in the child table are also automatically deleted. Use with extreme caution! This can lead to significant data loss if not carefully designed.
 * ON DELETE SET NULL: If a row in the parent table is deleted, the foreign key column(s) in the child table are set to NULL. Requires the foreign key column(s) in the child table to be nullable.
 * ON DELETE SET DEFAULT: (Less common, not universally supported) Sets the foreign key column(s) to their default value.
 * ON UPDATE: Similar options (NO ACTION, RESTRICT, CASCADE, SET NULL, SET DEFAULT) apply to UPDATE operations on the parent key.
Example with ON DELETE CASCADE:
CREATE TABLE Books (
    book_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    author_id INT NOT NULL,
    -- If an author is deleted, all their books are also deleted
    FOREIGN KEY (author_id) REFERENCES Authors(author_id) ON DELETE CASCADE
);

-- If author_id = 1 is deleted from Authors, all books by author_id = 1 will be deleted from Books.

Real-world scenario (JPA/Hibernate & Spring Boot):
In ORMs like JPA/Hibernate, you define these relationships and cascading behaviors using annotations (e.g., @OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)). When using JDBC directly with Spring Boot, you'd write the CREATE TABLE and ALTER TABLE statements yourself to define these constraints. ORMs simplify this by mapping Java objects to database tables and handling the underlying SQL generation for relationships and basic CRUD operations, often managing foreign keys implicitly.
🔗 9. Joins and Relationships
Relational databases are powerful because they allow you to store related data in separate tables and then combine them using joins.
Relationships: One-to-One, One-to-Many, Many-to-Many
 * One-to-One (1:1): Each record in Table A relates to exactly one record in Table B, and vice-versa.
   * Example: A Users table and a UserProfiles table (where profile data is separated for performance or optionality). Often implemented by making the foreign key in the child table also its primary key.
 * One-to-Many (1:M): Each record in Table A can relate to one or more records in Table B, but each record in Table B relates to only one record in Table A. This is the most common type of relationship.
   * Example: Authors (one) to Books (many). An author can write many books, but each book has one author. Implemented by a foreign key in the "many" side table pointing to the "one" side's primary key.
 * Many-to-Many (M:N): Each record in Table A can relate to one or more records in Table B, and each record in Table B can relate to one or more records in Table A.
   * Example: Books (many) to Genres (many). A book can have multiple genres, and a genre can apply to multiple books. Implemented using an intermediate table (also called a join table or bridge table) that holds foreign keys from both tables.
Types of Joins
Joins are used to combine rows from two or more tables based on a related column between them.
INNER JOIN
Returns only the rows where there is a match in both tables. It's the most common type of join.
Syntax:
SELECT columns
FROM table1
INNER JOIN table2 ON table1.matching_column = table2.matching_column;

Example:
-- Get book titles along with their author's first and last names
SELECT B.title, A.first_name, A.last_name
FROM Books AS B
INNER JOIN Authors AS A ON B.author_id = A.author_id;

LEFT JOIN (or LEFT OUTER JOIN)
Returns all rows from the left table, and the matching rows from the right table. If there's no match in the right table, NULL values are returned for the right table's columns.
Syntax:
SELECT columns
FROM table1
LEFT JOIN table2 ON table1.matching_column = table2.matching_column;

Example:
-- Get all authors and any books they've written. If an author has no books, they'll still appear.
SELECT A.first_name, A.last_name, B.title
FROM Authors AS A
LEFT JOIN Books AS B ON A.author_id = B.author_id;

(This will show authors with NULL in the title column if they haven't written any books in our Books table.)
RIGHT JOIN (or RIGHT OUTER JOIN)
Returns all rows from the right table, and the matching rows from the left table. If there's no match in the left table, NULL values are returned for the left table's columns. (Less common than LEFT JOIN, as you can usually achieve the same result by swapping table order and using LEFT JOIN).
Syntax:
SELECT columns
FROM table1
RIGHT JOIN table2 ON table1.matching_column = table2.matching_column;

Example:
-- Get all books and their authors. If a book has no matching author (shouldn't happen with FK), it would still appear.
SELECT B.title, A.first_name, A.last_name
FROM Books AS B
RIGHT JOIN Authors AS A ON B.author_id = A.author_id; -- Same as LEFT JOIN Authors B ON B.author_id = A.author_id

FULL OUTER JOIN (or FULL JOIN)
Returns all rows when there is a match in either the left or the right table. If there's no match, NULL values are returned for the columns from the non-matching side.
Syntax:
SELECT columns
FROM table1
FULL OUTER JOIN table2 ON table1.matching_column = table2.matching_column;

Note: MySQL does not directly support FULL OUTER JOIN. You can simulate it using a LEFT JOIN UNION ALL RIGHT JOIN (and add a WHERE clause to filter out duplicate rows that match in both). PostgreSQL and SQL Server support it directly.
Example (Conceptual for MySQL, actual for PostgreSQL/SQL Server):
-- Get all authors and all books. Show authors without books and books without authors (if any).
SELECT A.first_name, A.last_name, B.title
FROM Authors AS A
FULL OUTER JOIN Books AS B ON A.author_id = B.author_id;

SELF JOIN
A table joined to itself. This is useful when you want to compare rows within the same table, often used for hierarchical data or finding relationships within one dataset.
Example: Imagine an Employees table with employee_id and manager_id. manager_id is a foreign key referencing employee_id within the same table.
CREATE TABLE Employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(100),
    manager_id INT,
    FOREIGN KEY (manager_id) REFERENCES Employees(employee_id)
);

INSERT INTO Employees VALUES
(1, 'Alice', NULL),
(2, 'Bob', 1),
(3, 'Charlie', 1),
(4, 'David', 2);

-- Find employees and their managers
SELECT E.employee_name AS Employee, M.employee_name AS Manager
FROM Employees AS E
INNER JOIN Employees AS M ON E.manager_id = M.employee_id;

CROSS JOIN
Returns the Cartesian product of the two tables. Every row from the first table is combined with every row from the second table. Rarely used directly in application queries, but sometimes for generating test data or specific analytical tasks.
Syntax:
SELECT columns
FROM table1
CROSS JOIN table2;

Example:
-- Imagine a table of colors and a table of sizes. A CROSS JOIN would give all color-size combinations.
SELECT c.color_name, s.size_value
FROM Colors AS c
CROSS JOIN Sizes AS s;

Join Conditions with GROUP BY
When using joins with GROUP BY, you typically group by columns from one or more tables to aggregate related data.
Example:
-- Count books per author, including authors with no books (using LEFT JOIN)
SELECT A.first_name, A.last_name, COUNT(B.book_id) AS NumberOfBooks
FROM Authors AS A
LEFT JOIN Books AS B ON A.author_id = B.author_id
GROUP BY A.author_id, A.first_name, A.last_name; -- Group by PK and name for completeness

Using Aliases in Join Queries
Aliases are almost essential in join queries for readability and to avoid ambiguity when columns with the same name exist in different tables (e.g., id column in multiple tables).
Example:
SELECT B.title AS BookTitle, A.first_name AS AuthorFirstName
FROM Books AS B
INNER JOIN Authors AS A ON B.author_id = A.author_id;

🔁 10. Many-to-Many Relationships
This is a critical concept for robust database design. Many-to-Many relationships cannot be directly represented with a single foreign key. They require an intermediary "join table" (also called a "bridge table" or "associative table").
Join Tables
A join table has at least two foreign keys, each referencing the primary key of one of the two tables involved in the M:N relationship.
Example: Books and Genres (A book can have multiple genres, and a genre can apply to multiple books).
 * Books table: book_id (PK), title, author_id, etc.
 * Genres table: genre_id (PK), genre_name
 * BookGenres (Join Table): book_id (FK to Books), genre_id (FK to Genres)
Foreign Keys and Composite Keys
The join table's primary key is typically a composite primary key consisting of the two foreign keys. This ensures that a specific combination of book and genre is unique in the BookGenres table.
Example Schema:
CREATE TABLE Genres (
    genre_id INT PRIMARY KEY AUTO_INCREMENT,
    genre_name VARCHAR(100) UNIQUE NOT NULL
);

CREATE TABLE BookGenres (
    book_id INT NOT NULL,
    genre_id INT NOT NULL,
    PRIMARY KEY (book_id, genre_id), -- Composite Primary Key
    FOREIGN KEY (book_id) REFERENCES Books(book_id) ON DELETE CASCADE,
    FOREIGN KEY (genre_id) REFERENCES Genres(genre_id) ON DELETE CASCADE
);

Insert and Retrieve Records Using Joins
Inserting Data into a Many-to-Many Relationship
You insert into the two main tables first, then into the join table to create the associations.
Example:
-- Insert Genres
INSERT INTO Genres (genre_name) VALUES ('Fantasy'), ('Mystery'), ('Science Fiction');

-- Assuming you have book_id for 'Harry Potter' (e.g., 3) and 'Murder on the Orient Express' (e.g., 1)

-- Associate 'Harry Potter' with 'Fantasy'
INSERT INTO BookGenres (book_id, genre_id)
VALUES (3, (SELECT genre_id FROM Genres WHERE genre_name = 'Fantasy'));

-- Associate 'Murder on the Orient Express' with 'Mystery'
INSERT INTO BookGenres (book_id, genre_id)
VALUES (1, (SELECT genre_id FROM Genres WHERE genre_name = 'Mystery'));

-- Associate 'Harry Potter' with another genre if applicable (e.g., 'Young Adult' if added)
-- INSERT INTO BookGenres (book_id, genre_id) VALUES (3, (SELECT genre_id FROM Genres WHERE genre_name = 'Young Adult'));

Retrieving Data from a Many-to-Many Relationship using Joins
You'll need at least two INNER JOINs: one to the join table, and one from the join table to the second related table.
Example:
-- Find all genres for 'Harry Potter and the Sorcerer''s Stone'
SELECT B.title, G.genre_name
FROM Books AS B
INNER JOIN BookGenres AS BG ON B.book_id = BG.book_id
INNER JOIN Genres AS G ON BG.genre_id = G.genre_id
WHERE B.title = 'Harry Potter and the Sorcerer''s Stone';

-- Find all books for the 'Mystery' genre
SELECT B.title, A.first_name, A.last_name
FROM Books AS B
INNER JOIN BookGenres AS BG ON B.book_id = BG.book_id
INNER JOIN Genres AS G ON BG.genre_id = G.genre_id
INNER JOIN Authors AS A ON B.author_id = A.author_id
WHERE G.genre_name = 'Mystery';

Real-world scenario (JPA/Hibernate & Spring Boot):
ORMs simplify M:N mapping significantly. You define @ManyToMany annotations on the entity classes, and the ORM automatically handles the creation of the join table and the associated CRUD operations. For example, in a Book entity, you'd have a Set<Genre> genres field annotated with @ManyToMany.
🔍 11. Views and Advanced Clauses
Creating and Replacing VIEWS
A view is a virtual table based on the result-set of a SQL query. A view contains rows and columns, just like a real table. The fields in a view are fields from one or more real tables in the database.
Purpose:
 * Security: Restrict access to certain columns or rows without granting direct table access.
 * Simplicity: Simplify complex queries (e.g., joins, aggregations) into a single, easy-to-use virtual table.
 * Consistency: Provide a consistent view of data, even if the underlying table structure changes (as long as the view's definition is updated).
Syntax (Create):
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;

Syntax (Replace - if exists, update it):
CREATE OR REPLACE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;

Example:
-- Create a view showing popular books (price > 15)
CREATE VIEW PopularBooks AS
SELECT book_id, title, price
FROM Books
WHERE price > 15.00;

-- Now you can query the view like a table
SELECT * FROM PopularBooks;

-- Create a view showing author details and their book count
CREATE VIEW AuthorBookCounts AS
SELECT A.first_name, A.last_name, COUNT(B.book_id) AS NumberOfBooks
FROM Authors AS A
LEFT JOIN Books AS B ON A.author_id = B.author_id
GROUP BY A.author_id, A.first_name, A.last_name;

SELECT * FROM AuthorBookCounts WHERE NumberOfBooks > 1;

Updatable vs Non-Updatable Views
 * Updatable Views: Views that allow INSERT, UPDATE, and DELETE operations on them, which in turn modify the underlying base table(s). Generally, simple views based on a single table with no aggregations, DISTINCT, GROUP BY, or complex joins are updatable.
 * Non-Updatable Views: Views that cannot be directly modified. These typically involve:
   * Aggregate functions (SUM, COUNT, AVG, etc.)
   * DISTINCT keyword
   * GROUP BY or HAVING clauses
   * UNION or UNION ALL
   * Joins (though some simple inner joins might be updatable under specific conditions in some RDBMS)
   * Subqueries in SELECT list
   * ORDER BY (in some RDBMS)
Practical Scenario: You might grant SELECT access to a view (which shows only non-sensitive data) but prevent direct access to the base tables.
HAVING, ROLLUP for Grouped Summaries
 * HAVING: (Already covered) Filters groups after aggregation.
 * ROLLUP (with GROUP BY): An extension to the GROUP BY clause that generates subtotals and grand totals for the aggregated columns. It's used for hierarchical summaries.
   Syntax:
   SELECT column1, column2, aggregate_function(column3)
FROM table_name
GROUP BY ROLLUP (column1, column2);

   Example:
   Consider sales data by year, quarter, month.
   GROUP BY ROLLUP (year, quarter, month) would generate:
   * Aggregates for each (year, quarter, month)
   * Subtotals for each (year, quarter)
   * Subtotals for each (year)
   * Grand total
   <!-- end list -->
   -- Assuming a sales table with sale_year, sale_month, and amount
SELECT
    COALESCE(CAST(sale_year AS CHAR), 'Total') AS Year,
    COALESCE(CAST(sale_month AS CHAR), 'Total') AS Month,
    SUM(amount) AS TotalSales
FROM Sales
GROUP BY ROLLUP(sale_year, sale_month);

   COALESCE is used to replace NULL values (which ROLLUP generates for the subtotal rows) with a more descriptive string like 'Total'.
Materialized Views (where supported)
A materialized view is a physical copy of the data from a query, stored on disk. Unlike a regular view (which is a virtual table and its data is generated on the fly), a materialized view's data is pre-calculated and stored.
Purpose:
 * Performance: Significantly improve query performance for complex, frequently run queries, especially those involving aggregations or joins over large datasets.
 * Data Warehousing: Common in data warehousing for summarizing data for reporting.
Trade-offs:
 * Staleness: The data in a materialized view can become stale if the underlying tables change. It needs to be refreshed periodically.
 * Storage Space: Consumes additional disk space.
 * Refresh Overhead: Refreshing can be resource-intensive, especially for large views.
Syntax (PostgreSQL example):
CREATE MATERIALIZED VIEW book_summary_mv AS
SELECT publication_year, COUNT(*) AS num_books, AVG(price) AS avg_price
FROM Books
GROUP BY publication_year;

-- To refresh the materialized view:
REFRESH MATERIALIZED VIEW book_summary_mv;

Note: MySQL does not have native materialized views. SQL Server has "indexed views" which are similar in concept.
⚙️ 12. SQL Modes and Strict Behavior
SQL modes affect the behavior of MySQL in various ways, particularly regarding data validation and error handling. While this section is primarily MySQL-centric, other RDBMS have similar configuration settings that dictate their strictness.
STRICT_TRANS_TABLES and how it affects validation
STRICT_TRANS_TABLES (Strict SQL mode for transactional tables) is a common SQL mode in MySQL. When enabled:
 * Invalid data for a column: If you try to insert an invalid value into a column (e.g., a string into an INT column, or a value too long for VARCHAR), MySQL will raise an error and roll back the transaction.
 * Missing NOT NULL columns: If you try to insert a new row without providing a value for a NOT NULL column that has no default, an error occurs.
 * Data truncated: For numeric columns, if a value is truncated (e.g., inserting 123.456 into a DECIMAL(5,2)), it will cause an error instead of a warning and truncation.
Without STRICT_TRANS_TABLES: MySQL might silently convert invalid values to the closest valid representation, truncate strings, or insert NULL or 0 for missing NOT NULL values, and issue a warning rather than an error. This can lead to silent data corruption and unexpected behavior.
Common SQL Modes and their Implications
 * STRICT_ALL_TABLES: Similar to STRICT_TRANS_TABLES but applies to all storage engines, including non-transactional ones.
 * ONLY_FULL_GROUP_BY: Enforces strict adherence to the SQL standard for GROUP BY clauses. All non-aggregated columns in the SELECT list must either be in the GROUP BY clause or be functionally dependent on GROUP BY columns (e.g., through a primary key). This prevents ambiguity in group results.
 * NO_ZERO_DATE / NO_ZERO_IN_DATE: Prohibits '0000-00-00' as a valid date or '00' for month/day parts in dates.
 * NO_ENGINE_SUBSTITUTION: Prevents automatic substitution of the specified storage engine if it's unavailable.
 * NO_AUTO_VALUE_ON_ZERO: Prevents AUTO_INCREMENT columns from generating a new value when 0 is inserted. A new value is generated only when NULL or DEFAULT is inserted.
Enabling/Disabling Modes for Dev vs Production
 * Development: You might start with more relaxed modes to quickly insert data during initial development. However, it's highly recommended to enable strict modes early to catch data issues.
 * Production: Always enable strict SQL modes in production environments. This ensures data integrity, consistency, and helps prevent unexpected behavior due to invalid data. It forces your application code to handle data correctly before it reaches the database.
How to set SQL modes (MySQL):
 * Globally (persistent across server restarts):
   SET GLOBAL sql_mode = 'STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION';

 * Session-level (for current connection only):
   SET SESSION sql_mode = 'ONLY_FULL_GROUP_BY,NO_ZERO_DATE';

 * You can get the current SQL mode:
   SELECT @@sql_mode;

Implications for Developers:
When working with ORMs or raw JDBC, understanding SQL modes is critical. If your application attempts an invalid insert in strict mode, it will receive an error, which you must handle gracefully. If not in strict mode, the database might silently accept "bad" data, leading to subtle bugs or corrupted reports later.
🔭 13. Window Functions
Window functions perform a calculation across a set of table rows that are related to the current row. Unlike aggregate functions (which collapse rows into a single result per group), window functions return a value for each row in the result set. They are incredibly powerful for analytical queries.
Use Cases for Analytical Queries
 * Calculating running totals or moving averages.
 * Ranking items within groups (e.g., top N sales per region).
 * Comparing a row's value to a preceding or succeeding row's value.
 * Calculating cumulative sums or percentages.
Syntax: OVER(), PARTITION BY, ORDER BY
The core of a window function is the OVER() clause, which defines the "window" or set of rows over which the function operates.
 * OVER(): Specifies that the function is a window function.
 * PARTITION BY column1, column2, ...: Divides the rows into groups (partitions). The window function is applied independently to each partition. (Similar to GROUP BY but doesn't collapse rows).
 * ORDER BY column1, column2, ...: Defines the order of rows within each partition. Crucial for ranking functions, running totals, and functions like LEAD/LAG.
General Syntax:
window_function(expression) OVER (
    [PARTITION BY column1, column2, ...]
    [ORDER BY column3 [ASC|DESC], column4 [ASC|DESC], ...]
    [ROWS/RANGE BETWEEN ...] -- Frame clause (advanced)
)

Functions: RANK(), DENSE_RANK(), ROW_NUMBER(), NTILE()
These are common ranking window functions.
 * ROW_NUMBER(): Assigns a unique, sequential integer to each row within its partition, starting from 1. No ties.
 * RANK(): Assigns a rank within its partition. If two rows have the same value, they get the same rank, and the next rank skips values. (e.g., 1, 2, 2, 4)
 * DENSE_RANK(): Assigns a rank within its partition. If two rows have the same value, they get the same rank, and the next rank is consecutive. (e.g., 1, 2, 2, 3)
 * NTILE(N): Divides the rows in the partition into N groups (tiles) and assigns a tile number (1 to N) to each row. Useful for percentile calculations.
Example:
-- Assuming a table 'Sales' with columns: product_id, sale_date, amount, region

-- Rank sales amount within each region
SELECT
    product_id,
    sale_date,
    amount,
    region,
    ROW_NUMBER() OVER (PARTITION BY region ORDER BY amount DESC) AS RowNumByRegionAmount,
    RANK() OVER (PARTITION BY region ORDER BY amount DESC) AS RankByRegionAmount,
    DENSE_RANK() OVER (PARTITION BY region ORDER BY amount DESC) AS DenseRankByRegionAmount
FROM Sales;

LEAD(), LAG(), FIRST_VALUE(), LAST_VALUE()
These functions help access values from other rows within the same window.
 * LEAD(column, offset, default): Returns the value of column from the row at offset rows after the current row in the window. default is returned if no such row exists.
 * LAG(column, offset, default): Returns the value of column from the row at offset rows before the current row in the window. default is returned if no such row exists.
 * FIRST_VALUE(column): Returns the value of column from the first row in the current window frame.
 * LAST_VALUE(column): Returns the value of column from the last row in the current window frame. (Be mindful of the default window frame for LAST_VALUE).
Example:
-- Find the previous day's sales for each product (assuming 'Sales' table with 'sale_date' and 'amount')
SELECT
    product_id,
    sale_date,
    amount,
    LAG(amount, 1, 0) OVER (PARTITION BY product_id ORDER BY sale_date) AS PreviousDaySales
FROM Sales;

-- Calculate the difference in sales from the previous day
SELECT
    product_id,
    sale_date,
    amount,
    amount - LAG(amount, 1, 0) OVER (PARTITION BY product_id ORDER BY sale_date) AS SalesChange
FROM Sales;

-- Get the first sale amount for each product
SELECT
    product_id,
    sale_date,
    amount,
    FIRST_VALUE(amount) OVER (PARTITION BY product_id ORDER BY sale_date) AS FirstSaleAmount
FROM Sales;

Note on LAST_VALUE: By default, the window frame for LAST_VALUE is RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW. To get the true last value of the entire partition, you often need to explicitly define the window frame as ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING.
🔁 14. Subqueries and Nested Queries
A subquery (or inner query or nested query) is a query embedded inside another SQL query. They can be used in various clauses of a SQL statement.
Scalar Subqueries
A scalar subquery returns a single value (one column, one row). It can be used anywhere a single value is expected, like in SELECT, WHERE, or HAVING clauses.
Example:
-- Get the book with the highest price
SELECT title, price
FROM Books
WHERE price = (SELECT MAX(price) FROM Books);

IN, EXISTS, ANY, ALL with Subqueries
These operators are commonly used with subqueries in WHERE clauses.
 * IN: Checks if a value is present in the result set of the subquery.
   * Pros: Readable, often performant for smaller result sets.
   * Cons: Can be slow if the subquery returns a very large number of rows.
 * EXISTS: Checks for the existence of any row returned by the subquery. It's often more efficient than IN for large subquery result sets because it stops processing as soon as it finds one matching row.
 * ANY / SOME: (Synonyms) Compares a value with any value returned by the subquery.
   * > ANY means greater than at least one value (equivalent to > MIN()).
   * < ANY means less than at least one value (equivalent to < MAX()).
 * ALL: Compares a value with all values returned by the subquery.
   * > ALL means greater than every value (equivalent to > MAX()).
   * < ALL means less than every value (equivalent to < MIN()).
Example (IN):
-- Find books by authors born before 1950
SELECT title
FROM Books
WHERE author_id IN (SELECT author_id FROM Authors WHERE birth_date < '1950-01-01');

Example (EXISTS):
-- Find authors who have written at least one book
SELECT first_name, last_name
FROM Authors AS A
WHERE EXISTS (SELECT 1 FROM Books AS B WHERE B.author_id = A.author_id); -- 1 is a common convention, could be *

Contrast: If you use IN here, it would be WHERE A.author_id IN (SELECT B.author_id FROM Books AS B). EXISTS is often preferred when the subquery only needs to confirm existence.
Example (ANY):
-- Find books whose price is greater than at least one book published in 1970
SELECT title, price
FROM Books
WHERE price > ANY (SELECT price FROM Books WHERE publication_year = 1970);

Example (ALL):
-- Find books whose price is greater than ALL books published in 1970 (i.e., greater than the max 1970 book price)
SELECT title, price
FROM Books
WHERE price > ALL (SELECT price FROM Books WHERE publication_year = 1970);

Correlated vs Non-Correlated Subqueries
 * Non-Correlated Subquery: The inner query executes independently and its result is used by the outer query. It runs only once. (All examples above so far are non-correlated).
 * Correlated Subquery: The inner query depends on the outer query for its values. It executes once for each row processed by the outer query. This can impact performance.
Example (Correlated Subquery):
-- Find authors who have written a book with a price higher than the average price of all their own books
SELECT A.first_name, A.last_name
FROM Authors AS A
WHERE EXISTS (
    SELECT 1
    FROM Books AS B
    WHERE B.author_id = A.author_id -- Correlated part: B.author_id depends on outer A.author_id
      AND B.price > (SELECT AVG(price) FROM Books WHERE author_id = A.author_id) -- Another correlation
);

Using Subqueries in FROM, WHERE, and SELECT Clauses
 * FROM Clause (Derived Tables): A subquery in the FROM clause acts like a temporary table. It must be given an alias.
   SELECT D.department_name, AVG(E.salary) AS AvgSalary
FROM (SELECT employee_id, salary, department_id FROM Employees WHERE status = 'Active') AS E -- Derived table
JOIN Departments AS D ON E.department_id = D.department_id
GROUP BY D.department_name;

 * WHERE Clause: (Most common) Used for filtering rows, as shown in previous IN, EXISTS, ANY, ALL examples.
 * SELECT Clause (Scalar Subqueries): Used to retrieve a single value for each row from a subquery. Can impact performance if the subquery is complex and runs for every row.
   SELECT
    B.title,
    B.price,
    (SELECT A.first_name FROM Authors AS A WHERE A.author_id = B.author_id) AS AuthorFirstName -- Scalar subquery
FROM Books AS B;

   (Note: A JOIN is generally more efficient for this specific case).
🧪 15. Transactions & Isolation
This is a critically important concept for data integrity and concurrency, especially in multi-user environments.
What is a Transaction?
A transaction is a single logical unit of work that contains one or more SQL statements. Either all the statements within the transaction are successfully completed and committed to the database, or none of them are. If any statement fails, the entire transaction is rolled back, and the database returns to its state before the transaction began.
Analogy: A bank transfer from Account A to Account B. This involves two steps:
 * Debit Account A.
 * Credit Account B.
   If step 1 succeeds but step 2 fails (e.g., system crash), you don't want Account A to be debited while Account B isn't credited. A transaction ensures both steps either complete or neither do.
BEGIN, COMMIT, ROLLBACK
 * BEGIN TRANSACTION / START TRANSACTION: Marks the beginning of a transaction. (Syntax varies slightly: BEGIN; in PostgreSQL, START TRANSACTION; in MySQL, BEGIN TRANSACTION; in SQL Server).
 * COMMIT: Saves all changes made within the transaction permanently to the database. Once committed, the changes are visible to other transactions.
 * ROLLBACK: Undoes all changes made within the transaction since the BEGIN statement. The database reverts to its state before the transaction started.
Example:
-- Start a transaction (MySQL/PostgreSQL)
START TRANSACTION;

-- SQL statements within the transaction
UPDATE Accounts SET balance = balance - 100 WHERE account_id = 123;
UPDATE Accounts SET balance = balance + 100 WHERE account_id = 456;

-- Check if any errors occurred, or if business logic dictates a rollback
-- IF (error_condition) THEN
    -- ROLLBACK;
-- ELSE
    COMMIT;
-- END IF;

ACID Properties
Transactions are designed to guarantee ACID properties:
 * Atomicity: (All or Nothing) A transaction is treated as a single, indivisible unit of work. Either all its operations complete successfully, or none of them do.
 * Consistency: A transaction brings the database from one valid state to another valid state. It ensures that data remains valid according to defined rules (constraints, triggers).
 * Isolation: The execution of concurrent transactions produces the same result as if they were executed sequentially. Each transaction is isolated from the effects of other concurrent transactions until it is committed.
 * Durability: Once a transaction is committed, its changes are permanent and survive system failures (e.g., power outages, crashes).
Isolation Levels
Isolation levels define the degree to which a transaction must be isolated from the effects of other concurrent transactions. Higher isolation levels provide more consistency but can reduce concurrency (and thus performance).
Common Phenomena that Isolation Levels Prevent:
 * Dirty Reads: Reading uncommitted changes made by another transaction. (Least isolated).
 * Non-Repeatable Reads: Rereading data within the same transaction yields different values because another committed transaction modified the data.
 * Phantom Reads: Rereading data within the same transaction yields different sets of rows because another committed transaction inserted or deleted rows matching the query's criteria.
Standard SQL Isolation Levels (from lowest to highest isolation):
 * READ UNCOMMITTED:
   * Allows dirty reads.
   * Rarely used in practice due to high risk of inconsistent data.
 * READ COMMITTED:
   * Prevents dirty reads.
   * Allows non-repeatable reads and phantom reads.
   * Common default for many RDBMS (e.g., PostgreSQL, SQL Server).
 * REPEATABLE READ:
   * Prevents dirty reads and non-repeatable reads.
   * Allows phantom reads (in some implementations, e.g., MySQL's default InnoDB REPEATABLE READ prevents phantom reads due to "next-key locking").
   * Common default for MySQL (InnoDB).
 * SERIALIZABLE:
   * Highest isolation level.
   * Prevents dirty reads, non-repeatable reads, and phantom reads.
   * Achieves full serializability, meaning concurrent transactions execute as if they were run one after another.
   * Can significantly impact performance due to heavy locking. Rarely used unless strict consistency is paramount.
Syntax (to set isolation level for a session):
-- MySQL
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;

-- PostgreSQL
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;

-- SQL Server
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;

Practical Scenarios (JPA/Hibernate & Spring Boot):
 * In Spring Boot, you manage transactions declaratively using @Transactional annotations. Spring abstracts away the BEGIN, COMMIT, ROLLBACK commands.
 * You can specify the isolation level for a transaction using @Transactional(isolation = Isolation.READ_COMMITTED).
 * Understanding these levels is crucial when dealing with concurrent user access, ensuring data consistency, and preventing race conditions in your application logic. For example, if you're decrementing inventory, you need to ensure that two users don't simultaneously grab the last item, leading to negative stock (this often requires higher isolation or explicit pessimistic/optimistic locking).
✅ 16. Best Practices and Optimization
Efficient database design and query writing are critical for application performance and scalability.
Indexing Basics: Primary, Unique, Composite Indexes
An index is a special lookup table that the database search engine can use to speed up data retrieval. Think of it like an index in a book.
 * How they work: Indexes store a sorted list of values from one or more columns, along with pointers to the actual data rows. This allows the database to quickly find specific rows without scanning the entire table.
 * Trade-offs:
   * Faster reads: Significantly speeds up SELECT queries, especially with WHERE clauses, JOIN conditions, and ORDER BY.
   * Slower writes: INSERT, UPDATE, DELETE operations become slower because the indexes also need to be updated.
   * Disk space: Indexes consume additional disk space.
Types of Indexes:
 * Primary Key Index: Automatically created when you define a PRIMARY KEY. It's a clustered index in SQL Server (data is physically ordered) or a B-tree index in MySQL/PostgreSQL.
 * Unique Index: Ensures all values in the indexed column(s) are unique. Also speeds up searches. Automatically created when you define a UNIQUE constraint.
 * Composite/Compound Index: An index on multiple columns (e.g., (last_name, first_name)). Useful for queries that filter or sort by these columns together. The order of columns in a composite index matters! For (A, B, C), it can speed up queries on A, (A, B), or (A, B, C), but not necessarily just B or C.
When to create indexes:
 * On columns frequently used in WHERE clauses.
 * On columns used in JOIN conditions.
 * On columns used in ORDER BY or GROUP BY clauses.
 * On foreign key columns (often good practice, sometimes created automatically by RDBMS).
Syntax:
CREATE INDEX index_name ON table_name (column1, column2, ...);
CREATE UNIQUE INDEX unique_idx_name ON table_name (column);

Example:
-- To speed up searches for authors by last name
CREATE INDEX idx_authors_lastname ON Authors (last_name);

-- To speed up joins or searches on specific book titles
CREATE INDEX idx_books_title ON Books (title);

-- Composite index for efficient lookup by author and title
CREATE INDEX idx_books_author_title ON Books (author_id, title);

Query Performance Tuning and EXPLAIN
Optimizing queries is an art and a science. The EXPLAIN (or EXPLAIN ANALYZE in PostgreSQL, EXPLAIN PLAN in Oracle, SET SHOWPLAN_ALL in SQL Server) statement is your most valuable tool.
Purpose of EXPLAIN: It shows you the execution plan of your SQL query – how the database will retrieve the data. This reveals:
 * Which indexes are used (or not used).
 * The order in which tables are joined.
 * Whether full table scans are performed.
 * Estimated number of rows processed.
Example (MySQL):
EXPLAIN SELECT B.title, A.first_name, A.last_name
FROM Books AS B
INNER JOIN Authors AS A ON B.author_id = A.author_id
WHERE A.last_name = 'King' AND B.publication_year > 1980;

Analyzing the EXPLAIN output allows you to identify bottlenecks and decide where to add indexes, rewrite queries, or refactor your schema.
Common Query Tuning Tips:
 * Avoid SELECT *: Select only the columns you need.
 * Filter early: Use WHERE clauses to reduce the dataset before joins or aggregations.
 * Optimize JOINs: Ensure join conditions use indexed columns.
 * Be careful with OR: OR can sometimes prevent index usage. Consider UNION ALL or IN instead.
 * Avoid functions on indexed columns in WHERE: WHERE YEAR(order_date) = 2023 prevents index use on order_date. Better: WHERE order_date BETWEEN '2023-01-01' AND '2023-12-31'.
 * Use LIMIT / OFFSET wisely: For large offsets, pagination can be slow as the database still has to process earlier rows.
Denormalization Trade-offs
 * Normalization: The process of organizing the columns and tables of a relational database to minimize data redundancy and improve data integrity. Normal Forms (1NF, 2NF, 3NF, BCNF, etc.) provide guidelines.
   * Pros: Reduces data duplication, ensures data consistency, easier to maintain.
   * Cons: Can lead to more complex queries (more joins) and potentially slower read performance for reporting.
 * Denormalization: Intentionally introducing redundancy into a database by adding duplicate data or grouping data to improve read performance.
   * Pros: Faster reads (fewer joins), simpler queries for specific reporting needs.
   * Cons: Increased data redundancy, higher risk of data inconsistency, slower writes (updates need to change data in multiple places), harder to maintain.
When to consider Denormalization:
 * For read-heavy reporting or analytics databases (data warehouses).
 * When normalized schema leads to unacceptably slow queries for critical paths.
 * When data consistency can be managed by triggers or application logic.
Example:
Instead of joining Orders and Customers every time to get the customer's name, you might denormalize by adding customer_name directly to the Orders table. This creates redundancy but speeds up queries that only need customer name and order details.
Normal Forms (1NF, 2NF, 3NF)
Database normalization is a process of organizing the fields and tables of a relational database to minimize data redundancy and eliminate anomalies (insertion, update, and deletion anomalies).
 * First Normal Form (1NF):
   * Each column must contain atomic (indivisible) values. No multi-valued attributes.
   * Each row must be unique.
   * Violation Example: A Customers table with a phone_numbers column containing "123-4567, 987-6543".
   * Solution: Create a separate CustomerPhones table.
 * Second Normal Form (2NF):
   * Must be in 1NF.
   * All non-key attributes must be fully functionally dependent on the entire primary key. (Applies to tables with composite primary keys).
   * Violation Example: An OrderDetails table (order_id, product_id as PK) with product_name and product_description. product_name depends only on product_id, not the full PK.
   * Solution: Move product_name and product_description to a separate Products table.
 * Third Normal Form (3NF):
   * Must be in 2NF.
   * No transitive dependencies: Non-key attributes should not depend on other non-key attributes.
   * Violation Example: A Customers table with zip_code and city, state. city and state are dependent on zip_code, which is not the primary key.
   * Solution: Create a separate ZipCodes table (zip_code PK, city, state).
Beyond 3NF, there are Boyce-Codd Normal Form (BCNF), 4NF, 5NF, etc., addressing more complex dependency scenarios. For most business applications, reaching 3NF is a good balance between normalization benefits and practical complexity.
🧠 17. Additional Concepts (If time allows)
These advanced features offer powerful capabilities for database management and application logic within the database itself.
Triggers
A trigger is a special kind of stored procedure that automatically executes (or "fires") when a specific event occurs in the database.
 * Events: INSERT, UPDATE, DELETE operations on a table.
 * Timing: BEFORE or AFTER the event.
 * Purpose: Enforce complex business rules, audit data changes, maintain derived data, implement custom logging.
Syntax (MySQL example):
DELIMITER //

CREATE TRIGGER before_book_insert
BEFORE INSERT ON Books
FOR EACH ROW
BEGIN
    IF NEW.price <= 0 THEN
        SET NEW.price = 0.01; -- Ensure price is never zero or negative
    END IF;
END;
//

DELIMITER ; -- Reset delimiter

Practical Scenarios:
 * Automatically update a last_modified timestamp column.
 * Validate data before insertion (e.g., ensure an order quantity doesn't exceed available stock).
 * Maintain an audit log of changes to sensitive data.
 * Implement complex cascading logic not handled by foreign keys.
Caution: Over-reliance on triggers can make debugging difficult (logic is hidden in the DB), and can lead to performance issues if not carefully designed.
Stored Procedures and Functions
 * Stored Procedure: A prepared SQL code that you can save and reuse. It can accept input parameters and return output parameters or result sets. Primarily for performing actions (DML or DDL).
   * Pros: Improved performance (pre-compiled), reduced network traffic, enhanced security (grant access to procedure, not underlying tables), centralize business logic.
   * Cons: Vendor-specific syntax (less portable), harder to version control than application code, can be a debugging challenge.
 * Function (User-Defined Function - UDF): Similar to a stored procedure, but it always returns a single scalar value (or a table in some RDBMS like SQL Server table-valued functions). Used primarily for computations.
Syntax (MySQL Stored Procedure Example):
DELIMITER //

CREATE PROCEDURE GetBooksByAuthor(IN authorId INT)
BEGIN
    SELECT title, publication_year, price
    FROM Books
    WHERE author_id = authorId;
END //

DELIMITER ;

-- Execute the procedure
CALL GetBooksByAuthor(1);

Syntax (PostgreSQL Function Example):
CREATE FUNCTION GetTotalBookPrice() RETURNS DECIMAL(10,2) AS $$
DECLARE
    total_price DECIMAL(10,2);
BEGIN
    SELECT SUM(price) INTO total_price FROM Books;
    RETURN total_price;
END;
$$ LANGUAGE plpgsql;

-- Execute the function
SELECT GetTotalBookPrice();

Practical Scenarios:
 * Complex reporting logic.
 * Batch processing operations.
 * Encapsulating sensitive operations for security.
 * Data migration scripts.
Real-world scenario (Spring Boot):
While ORMs often reduce the need for raw stored procedures, they can still be invoked from Spring Boot applications using JdbcTemplate or by mapping them to JPA native queries. This is common for legacy systems or highly optimized reporting.
User-Defined Variables (Session Variables)
Variables that allow you to store a value and reference it later within the same session. Syntax varies.
 * MySQL: SET @variable_name = value;
 * SQL Server: DECLARE @variable_name data_type; SET @variable_name = value;
 * PostgreSQL: Not directly supported as session variables in the same way, but DO blocks or functions can use variables.
Example (MySQL):
SET @min_price = 12.00;

SELECT title, price
FROM Books
WHERE price > @min_price;

SET @author_name_search = 'King';
SELECT * FROM Authors WHERE last_name = @author_name_search;

Caution: Session variables are session-specific and not thread-safe in a multi-threaded application. Use with care.
CTEs (Common Table Expressions)
A Common Table Expression (CTE) is a named temporary result set that you can reference within a single SQL statement (e.g., SELECT, INSERT, UPDATE, DELETE). They make complex queries more readable and manageable by breaking them into logical, named steps.
Syntax:
WITH cte_name AS (
    -- Your subquery definition
    SELECT column1, column2
    FROM table_name
    WHERE condition
)
SELECT *
FROM cte_name
WHERE another_condition;

Example:
-- Find the average price of books for authors who have more than 2 books
WITH AuthorBookCounts AS (
    SELECT author_id, COUNT(book_id) AS num_books
    FROM Books
    GROUP BY author_id
    HAVING COUNT(book_id) > 2
)
SELECT A.first_name, A.last_name, AVG(B.price) AS AveragePrice
FROM Authors AS A
JOIN AuthorBookCounts AS ABC ON A.author_id = ABC.author_id
JOIN Books AS B ON A.author_id = B.author_id
GROUP BY A.author_id, A.first_name, A.last_name;

Benefits:
 * Readability: Break down complex queries into smaller, logical units.
 * Reusability: A CTE can be referenced multiple times within the same query.
 * Recursion: Essential for recursive queries (see next).
Recursive Queries (WITH RECURSIVE)
Used to query hierarchical data (e.g., organizational charts, bill of materials, network paths) where the depth of the hierarchy is unknown. Available in PostgreSQL, SQL Server, and MySQL 8+.
Syntax (PostgreSQL/MySQL):
WITH RECURSIVE cte_name AS (
    -- Anchor member (initial query that starts the recursion)
    SELECT initial_columns FROM initial_table WHERE initial_condition

    UNION ALL

    -- Recursive member (references cte_name and processes subsequent levels)
    SELECT next_level_columns FROM another_table
    JOIN cte_name ON join_condition_for_recursion
    WHERE recursive_condition
)
SELECT * FROM cte_name;

Example (Finding all subordinates in an Employees table):
-- Assuming Employees table with employee_id, employee_name, manager_id
WITH RECURSIVE Subordinates AS (
    -- Anchor: Find the starting employee (e.g., the CEO)
    SELECT employee_id, employee_name, manager_id, 0 AS level
    FROM Employees
    WHERE employee_name = 'Alice' -- Let's assume Alice is the top manager

    UNION ALL

    -- Recursive part: Find employees whose manager is in the previous level of Subordinates
    SELECT e.employee_id, e.employee_name, e.manager_id, s.level + 1
    FROM Employees AS e
    INNER JOIN Subordinates AS s ON e.manager_id = s.employee_id
)
SELECT * FROM Subordinates;

JSON Support in Modern SQL (e.g., MySQL, PostgreSQL)
Modern RDBMS increasingly support JSON data types and functions, allowing you to store, query, and manipulate semi-structured JSON data directly within your database. This bridges the gap between relational and NoSQL capabilities.
 * MySQL (8.0+): JSON data type, JSON_EXTRACT, JSON_SET, JSON_ARRAY, JSON_OBJECT, etc.
 * PostgreSQL (9.2+): json and jsonb data types (jsonb is binary, more efficient for querying), ->, ->>, #>,#>> operators, json_each, jsonb_pretty, etc.
 * SQL Server (2016+): FOR JSON, OPENJSON, JSON_VALUE, JSON_QUERY.
Example (MySQL):
-- Create a table with a JSON column
CREATE TABLE Products (
    product_id INT PRIMARY KEY,
    name VARCHAR(255),
    details JSON
);

-- Insert data with JSON
INSERT INTO Products (product_id, name, details) VALUES
(1, 'Laptop', '{"brand": "Dell", "specs": {"cpu": "i7", "ram_gb": 16}, "features": ["lightweight", "ssd"]}');

-- Query JSON data
SELECT name, JSON_EXTRACT(details, '$.brand') AS Brand
FROM Products
WHERE JSON_EXTRACT(details, '$.specs.ram_gb') > 8;

-- Update JSON data
UPDATE Products
SET details = JSON_SET(details, '$.price', 1200.00)
WHERE product_id = 1;

Example (PostgreSQL with jsonb):
CREATE TABLE Orders (
    order_id SERIAL PRIMARY KEY,
    customer_info JSONB,
    order_details JSONB[] -- Array of JSON objects
);

INSERT INTO Orders (customer_info, order_details) VALUES
(
    '{"name": "John Doe", "email": "john@example.com"}',
    ARRAY['{"product": "Laptop", "qty": 1, "price": 1200.00}'::jsonb, '{"product": "Mouse", "qty": 2, "price": 25.00}'::jsonb]
);

-- Query JSON data
SELECT customer_info ->> 'name' AS CustomerName
FROM Orders
WHERE customer_info ->> 'email' = 'john@example.com';

-- Access nested JSON
SELECT order_details[1] ->> 'product' AS FirstProductInOrder -- Array index starts at 1
FROM Orders;

Practical Scenarios:
 * Storing flexible attributes for products, users, or documents that don't fit a rigid relational schema.
 * Integrating with microservices that might produce or consume JSON data.
 * Building "hybrid" databases that combine structured and semi-structured data.
This comprehensive explanation covers the vast majority of SQL features and concepts required for mastery in backend development, data science, analytics, and system design. Remember that hands-on practice is crucial for solidifying this knowledge. Experiment with different queries, build your own schemas, and work through various problem sets to truly achieve mastery.
