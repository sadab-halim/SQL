# SQL

## SQL Basics
### Concept Summary
SQL (Structured Query Language) is a standard language for managing and manipulating relational databases. RDBMS (Relational Database Management System) like MySQL, PostgreSQL, Oracle, SQL Server store data in tables with predefined schemas and relationships. NoSQL databases (e.g., MongoDB, Cassandra) offer flexible schemas and are often used for unstructured or semi-structured data, emphasizing scalability and availability over strict consistency.

### Best Practices/Pitfalls
- **Best Practice**: Understand the fundamental concepts of relational data before diving into syntax.
- **Pitfall**: Confusing SQL with general programming languages; SQL is declarative, focusing on what data to retrieve or modify, not how.

## CRUD Operations
### Concept Summary
CRUD stands for Create, Read, Update, and Delete – the four basic functions of persistent storage. In SQL, these correspond to `INSERT`, `SELECT`, `UPDATE`, and `DELETE` statements.
- `SELECT`: Retrieves data from one or more tables.
- `INSERT`: Adds new rows of data to a table.
- `UPDATE`: Modifies existing data in a table.
- `DELETE`: Removes rows from a table. `WHERE` clause filters rows based on specified conditions. `LIMIT` restricts the number of rows returned. `ORDER` BY sorts the result set.

### Example Queries
Assume a table named `Employees` with columns `EmployeeID` (INT), `FirstName` (VARCHAR), `LastName` (VARCHAR), `HireDate` (DATE), `Salary` (DECIMAL).
```
-- SELECT
SELECT * FROM Employees; -- Selects all columns and all rows
SELECT FirstName, LastName FROM Employees WHERE Salary > 50000; -- Selects specific columns with a condition
SELECT EmployeeID, FirstName FROM Employees ORDER BY LastName ASC LIMIT 10; -- Orders and limits

-- INSERT
INSERT INTO Employees (FirstName, LastName, HireDate, Salary)
VALUES ('John', 'Doe', '2023-01-15', 60000.00);

-- UPDATE
UPDATE Employees
SET Salary = 65000.00
WHERE FirstName = 'John' AND LastName = 'Doe';

-- DELETE
DELETE FROM Employees
WHERE Salary < 30000;
```

### Best Practices/Pitfalls
- **Best Practice**: Always use a `WHERE` clause with `UPDATE` and `DELETE` statements unless you intend to affect all rows.
- **Best Practice**: Be specific with `SELECT` columns (`SELECT col1, col2`) rather than `SELECT *` for better performance and clarity in production environments.
- **Pitfall**: Forgetting `WHERE` clause in `UPDATE` or `DELETE`, leading to unintended data loss.

## Functions and Expressions
### Concept Summary
SQL functions perform calculations, manipulate data, and format output
- **Aggregate Functions**: Operate on a set of rows and return a single summary value (e.g., `COUNT()`, `SUM()`, `AVG()`, `MIN()`, `MAX()`).
- **Scalar Functions**: Operate on a single value and return a single value (e.g., `UPPER()`, `LOWER()`, `NOW()`, `LEN()`/`LENGTH()`, `COALESCE()`). COALESCE() returns the first non-NULL expression in a list

### Example Queries
Assume the `Employees` table.
```sql
-- Aggregate Functions
SELECT COUNT(*) AS TotalEmployees FROM Employees;
SELECT SUM(Salary) AS TotalSalary, AVG(Salary) AS AverageSalary FROM Employees;
SELECT MIN(HireDate) AS EarliestHire, MAX(Salary) AS HighestSalary FROM Employees;

-- Scalar Functions
SELECT UPPER(FirstName) AS UpperFirstName, LOWER(LastName) AS LowerLastName FROM Employees WHERE EmployeeID = 1;
SELECT NOW() AS CurrentDateTime; -- Returns current date and time
SELECT LENGTH(FirstName) AS FirstNameLength FROM Employees WHERE EmployeeID = 1; -- LEN() for SQL Server, LENGTH() for MySQL/PostgreSQL
SELECT COALESCE(NULL, 'Default Value', 'Another Value') AS CoalescedValue; -- Returns 'Default Value'
SELECT COALESCE(Salary, 0) AS EmployeeSalary FROM Employees; -- Replaces NULL salaries with 0
```

### Best Practices/Pitfalls
- **Best Practice**: Use aliases (AS) for aggregate function results to make the output more readable.
- **Pitfall**: Using aggregate functions directly in the `WHERE` clause (they belong in `HAVING`)

## Joins
### Concept Summary
Joins combine rows from two or more tables based on a related column between them.
- `INNER JOIN`: Returns only the rows where there is a match in both tables.
- `LEFT JOIN` (or `LEFT OUTER JOIN`): Returns all rows from the left table, and the matching rows from the right table. If there's no match, `NULL` values are returned for the right table's columns.
- `RIGHT JOIN` (or `RIGHT OUTER JOIN`): Returns all rows from the right table, and the matching rows from the left table. If there's no match, `NULL` values are returned for the left table's columns.
- `FULL OUTER JOIN`: Returns all rows when there is a match in one of the tables. If there's no match, `NULL` values are returned for the non-matching table's columns. (Not supported in MySQL, often emulated with `UNION` of `LEFT JOIN` and `RIGHT JOIN`).
- `SELF JOIN`: A table is joined with itself. Useful for comparing rows within the same table.
- `CROSS JOIN`: Returns the Cartesian product of the two tables (every row from the first table combined with every row from the second table). Rarely used for data retrieval, more for generating combinations

### Example Queries
Assume a `Departments` table: `DepartmentID` (INT), `DepartmentName` (VARCHAR).
`Employees` table has a `DepartmentID` (INT) foreign key.

```sql
-- INNER JOIN
SELECT E.FirstName, E.LastName, D.DepartmentName
FROM Employees E
INNER JOIN Departments D ON E.DepartmentID = D.DepartmentID;

-- LEFT JOIN
SELECT E.FirstName, E.LastName, D.DepartmentName
FROM Employees E
LEFT JOIN Departments D ON E.DepartmentID = D.DepartmentID; -- Shows all employees, even if they don't have a department

-- RIGHT JOIN
SELECT E.FirstName, E.LastName, D.DepartmentName
FROM Employees E
RIGHT JOIN Departments D ON E.DepartmentID = D.DepartmentID; -- Shows all departments, even if no employees are in them

-- FULL OUTER JOIN (Syntax varies by DB. Example for PostgreSQL/SQL Server)
-- SELECT E.FirstName, E.LastName, D.DepartmentName
-- FROM Employees E
-- FULL OUTER JOIN Departments D ON E.DepartmentID = D.DepartmentID;

-- SELF JOIN (Find employees who earn more than their manager)
-- Assume Employees table has ManagerID (INT) column, which is also an EmployeeID
SELECT E1.FirstName AS EmployeeName, E1.Salary AS EmployeeSalary,
       E2.FirstName AS ManagerName, E2.Salary AS ManagerSalary
FROM Employees E1
INNER JOIN Employees E2 ON E1.ManagerID = E2.EmployeeID
WHERE E1.Salary > E2.Salary;

-- CROSS JOIN
SELECT E.FirstName, D.DepartmentName
FROM Employees E
CROSS JOIN Departments D; -- Every employee combined with every department
```

### Best Practices/Pitfalls
- **Best Practice**: Always use aliases for table names in joins to improve readability and avoid ambiguity, especially with self-joins.
- **Best Practice**: Be mindful of the join type. Choosing the wrong join can lead to missing or excessive data.
- **Performance**: Joins can be resource-intensive, especially on large tables. Ensure appropriate indexes are on the join columns.
- **Pitfall**: Joining on non-indexed columns can severely degrade performance

## Filtering and Grouping
### Concept Summary
- `GROUP BY`: Groups rows that have the same values in specified columns into summary rows. Often used with aggregate functions.
- `HAVING`: Filters groups based on a specified condition. It's like `WHERE` but for groups, applied after GROUP BY.
- `WHERE` vs `HAVING`: `WHERE` filters individual rows before grouping and aggregation. `HAVING` filters groups after grouping and aggregation

### Example Queries
Assume `Employees` and `Departments` tables

```sql
-- GROUP BY with Aggregate
SELECT D.DepartmentName, COUNT(E.EmployeeID) AS NumberOfEmployees
FROM Employees E
INNER JOIN Departments D ON E.DepartmentID = D.DepartmentID
GROUP BY D.DepartmentName;

-- GROUP BY with HAVING
SELECT D.DepartmentName, AVG(E.Salary) AS AverageSalary
FROM Employees E
INNER JOIN Departments D ON E.DepartmentID = D.DepartmentID
GROUP BY D.DepartmentName
HAVING AVG(E.Salary) > 70000; -- Departments where average salary is > 70000

-- Multi-level grouping
SELECT D.DepartmentName, E.HireDate, COUNT(E.EmployeeID) AS EmployeesHired
FROM Employees E
INNER JOIN Departments D ON E.DepartmentID = D.DepartmentID
GROUP BY D.DepartmentName, E.HireDate
ORDER BY D.DepartmentName, E.HireDate;
```

### Best Practices/Pitfalls
- **Best Practice**: Understand the order of operations: `FROM -> JOIN -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY -> LIMIT`.
- **Best Practice**: Use `WHERE` to filter as much data as possible before grouping for performance.
- **Pitfall**: Accidentally using `WHERE` with an aggregate function.

## Subqueries and Nested Queries
### Concept Summary
A subquery (or inner query) is a query nested inside another SQL query (outer query).
- **Scalar Subquery**: Returns a single value. Can be used anywhere a single value is expected.
- **Row Subquery**: Returns a single row with one or more columns.
- **Table Subquery**: Returns a table (multiple rows and columns).
- **Correlated Subquery**: Executes once for each row processed by the outer query. The inner query depends on the outer query.
- **Non-correlated Subquery**: Executes independently of the outer query. Its result is then used by the outer query.
- `EXISTS`: Used to test for the existence of rows in a subquery. Returns TRUE if the subquery returns any rows.
- `IN`: Used to check if a value is present in a list of values returned by a subquery.
- `ANY`/`SOME`: Returns TRUE if any value in the subquery result satisfies the condition.
- `ALL`: Returns TRUE if all values in the subquery result satisfy the condition.

### Example Queries
Assume `Employees` table with `DepartmentID` and `Salary`
```sql
-- Scalar Subquery (Find employees earning more than the average salary)
SELECT FirstName, LastName, Salary
FROM Employees
WHERE Salary > (SELECT AVG(Salary) FROM Employees);

-- Non-correlated Subquery with IN (Find employees in departments located in 'New York')
-- Assume Departments table has Location (VARCHAR)
SELECT E.FirstName, E.LastName
FROM Employees E
WHERE E.DepartmentID IN (SELECT D.DepartmentID FROM Departments D WHERE D.Location = 'New York');

-- Correlated Subquery (Find employees who earn more than the average salary in their own department)
SELECT E1.FirstName, E1.LastName, E1.Salary, E1.DepartmentID
FROM Employees E1
WHERE E1.Salary > (SELECT AVG(E2.Salary)
                   FROM Employees E2
                   WHERE E2.DepartmentID = E1.DepartmentID); -- Correlated by E1.DepartmentID

-- EXISTS (Find departments that have at least one employee)
SELECT D.DepartmentName
FROM Departments D
WHERE EXISTS (SELECT 1 FROM Employees E WHERE E.DepartmentID = D.DepartmentID);

-- ALL (Find employees whose salary is higher than all salaries in Department 3)
SELECT FirstName, LastName, Salary
FROM Employees
WHERE Salary > ALL (SELECT Salary FROM Employees WHERE DepartmentID = 3);
```

### Best Practices/Pitfalls
- **Best Practice**: For simple `IN` checks, a `JOIN` often performs better than a subquery.
- **Best Practice**: Use `EXISTS` for checking existence, as it stops as soon as it finds a match.
- **Pitfall**: Correlated subqueries can be slow on large datasets due to repeated execution. Consider rewriting with joins or CTEs if possible.

## Set Operations
### Concept Summary
Set operations combine the results of two or more `SELECT` statements. The number and data types of columns in all `SELECT` statements must be the same.
- `UNION`: Combines the result sets and removes duplicate rows.
- `UNION ALL`: Combines the result sets and keeps all duplicate rows.
- `INTERSECT`: Returns only the rows that are present in both result sets. (Not supported in MySQL, often emulated with `INNER JOIN`).
- `EXCEPT` **(or** `MINUS` **in Oracle)**: Returns rows from the first result set that are not present in the second result set. (Not supported in MySQL, often emulated with `LEFT JOIN` and `WHERE IS NULL`).

### Example Queries
Assume `Employees` table and a `Contractors` table with similar columns: `ID`, `Name`, `Role`.

```sql
-- UNION (Unique names from Employees and Contractors)
SELECT FirstName AS Name FROM Employees
UNION
SELECT Name FROM Contractors;

-- UNION ALL (All names, including duplicates)
SELECT FirstName AS Name FROM Employees
UNION ALL
SELECT Name FROM Contractors;

-- INTERSECT (Example for PostgreSQL/SQL Server - find names that are both employees and contractors)
-- SELECT FirstName AS Name FROM Employees
-- INTERSECT
-- SELECT Name FROM Contractors;

-- Emulating INTERSECT in MySQL/databases without it
SELECT E.FirstName
FROM Employees E
INNER JOIN Contractors C ON E.FirstName = C.Name;

-- EXCEPT (Example for PostgreSQL/SQL Server - find names that are employees but not contractors)
-- SELECT FirstName AS Name FROM Employees
-- EXCEPT
-- SELECT Name FROM Contractors;

-- Emulating EXCEPT in MySQL/databases without it
SELECT E.FirstName
FROM Employees E
LEFT JOIN Contractors C ON E.FirstName = C.Name
WHERE C.Name IS NULL;
```

### Best Practices/Pitfalls
- **Best Practice**: Ensure matching column count and data types for all queries involved in set operations.
- **Best Practice**: Use UNION ALL if duplicates are acceptable and performance is critical, as UNION incurs an overhead for duplicate removal.
- **Pitfall**: Forgetting the column type compatibility, leading to errors

## Data Modeling & Table Management
### Concept Summary
- `CREATE TABLE`: Defines a new table in the database.
- `ALTER TABLE`: Modifies an existing table structure (add/drop columns, change data types, add/drop constraints).
- `DROP TABLE`: Deletes an existing table and all its data.
- **Primary Key**: Uniquely identifies each record in a table. Enforces entity integrity. Typically indexed automatically.
- **Foreign Key**: A column (or set of columns) that refers to the primary key in another table. Establishes relationships between tables. Enforces referential integrity.
- `NOT NULL`: Ensures a column cannot contain NULL values.
- `DEFAULT`: Assigns a default value to a column if no value is explicitly provided during insertion.
- `UNIQUE`: Ensures all values in a column are distinct. Can be combined with NOT NULL.
- `CHECK`: Enforces a condition on the values in a column.
- `AUTO_INCREMENT` **(or** `IDENTITY` **in SQL Server**, `SERIAL` **in PostgreSQL**): Automatically generates unique numbers for new records.

### Example Queries
```sql
-- CREATE TABLE
CREATE TABLE Products (
    ProductID INT PRIMARY KEY AUTO_INCREMENT, -- AUTO_INCREMENT for MySQL
    ProductName VARCHAR(255) NOT NULL UNIQUE,
    Price DECIMAL(10, 2) DEFAULT 0.00,
    StockQuantity INT CHECK (StockQuantity >= 0),
    CategoryID INT,
    FOREIGN KEY (CategoryID) REFERENCES Categories(CategoryID)
);

CREATE TABLE Categories (
    CategoryID INT PRIMARY KEY AUTO_INCREMENT,
    CategoryName VARCHAR(100) NOT NULL UNIQUE
);

-- ALTER TABLE (Add a new column)
ALTER TABLE Employees
ADD COLUMN Email VARCHAR(255);

-- ALTER TABLE (Modify column data type)
ALTER TABLE Employees
MODIFY COLUMN Email VARCHAR(100) NOT NULL; -- Syntax varies (e.g., ALTER COLUMN in SQL Server)

-- ALTER TABLE (Add a constraint)
ALTER TABLE Employees
ADD CONSTRAINT UQ_EmployeeEmail UNIQUE (Email);

-- ALTER TABLE (Drop a column)
ALTER TABLE Employees
DROP COLUMN Email;

-- DROP TABLE
DROP TABLE Products;
```

### Best Practices/Pitfalls
- **Best Practice**: Define appropriate constraints at table creation. They help maintain data integrity.
- **Best Practice**: Always think about relationships between tables and define foreign keys.
- **Pitfall**: Dropping tables or columns in a production environment without proper backup and impact analysis.
- **Pitfall**: Over-constraining a table, which can make data insertion difficult, or under-constraining, leading to inconsistent data.

## Indexes and Performance
### Concept Summary
An index is a special lookup table that the database search engine can use to speed up data retrieval. It's similar to the index in a book.
- **Clustered Index**: Determines the physical order of data in the table. A table can have only one clustered index. Often created automatically on the Primary Key.
- **Non-Clustered Index**: Stores data in a logical order, but the physical order of data remains unchanged. A table can have multiple non-clustered indexes.
- **Composite Index**: An index on multiple columns

**When to use indexes (and when not to)**: <br>
- **Use when**: Columns frequently used in `WHERE` clauses, `JOIN` conditions, `ORDER BY` clauses, `GROUP BY` clauses. Columns with high cardinality (many distinct values).
- **Don't use when**: Small tables. Columns with very low cardinality (e.g., boolean flags). Columns with frequent write operations (indexes add overhead to `INSERT`, `UPDATE`, `DELETE`).

**Index impact on read/write performance**:
- **Read Performance (SELECT)**: Significantly improves query speed by reducing the amount of data scanned.
- **Write Performance (INSERT, UPDATE, DELETE)**: Can slightly degrade performance because the index itself must also be updated.

### Example Queries
```sql
-- Create a non-clustered index on a single column
CREATE INDEX IX_Employees_LastName ON Employees (LastName);

-- Create a composite index
CREATE INDEX IX_Employees_DepartmentSalary ON Employees (DepartmentID, Salary);

-- Drop an index (Syntax varies by DB)
-- MySQL: DROP INDEX IX_Employees_LastName ON Employees;
-- SQL Server: DROP INDEX IX_Employees_LastName ON Employees.Employees;
-- PostgreSQL: DROP INDEX IX_Employees_LastName;
```

### Best Practices/Pitfalls
- **Best Practice**: Analyze query patterns. Index columns that are frequently used in `WHERE`, `JOIN`, `ORDER BY`, and `GROUP BY` clauses.
- **Best Practice**: Prioritize indexing primary and foreign keys.
- **Best Practice**: Avoid over-indexing. Too many indexes can slow down writes and consume excessive disk space.
- **Pitfall**: Creating indexes on columns with low cardinality (e.g., gender, status with only a few values), as it might not offer significant performance gains.
- **Pitfall**: Not regularly reviewing and maintaining indexes

## Transactions and Concurrency
### Concept Summary
A transaction is a single logical unit of work, comprising one or more SQL statements. Transactions ensure data integrity and reliability.
- **ACID Properties**:
    - **Atomicity**: All operations within a transaction either complete successfully (commit) or none do (rollback). "All or nothing."
    - **Consistency**: A transaction brings the database from one valid state to another. Data remains consistent with rules and constraints.
    - **Isolation**: Concurrent transactions are executed in isolation, meaning one transaction's partial results are not visible to others.
    - **Durability**: Once a transaction is committed, its changes are permanent and survive system failures.
- `BEGIN TRANSACTION` **(or ** `START TRANSACTION`**)**: Marks the beginning of a transaction.
- `COMMIT`: Saves all changes made during the transaction to the database permanently.
- `ROLLBACK`: Undoes all changes made during the transaction, restoring the database to its state before the transaction began
- **Isolation Levels**: Define the degree to which one transaction must be isolated from the actions of other concurrent transactions
  - `READ UNCOMMITTED`: Allows reading uncommitted changes (dirty reads). Lowest isolation, highest concurrency, but prone to errors
  - `READ COMMITTED`: Prevents dirty reads. Reads only committed data. (Default for many databases)
  - `REPEATABLE READ`: Prevents dirty reads and non-repeatable reads (reading the same data twice in a transaction yields different results). Phantom reads can still occur.
  - `SERIALIZABLE`: Highest isolation level. Prevents dirty reads, non-repeatable reads, and phantom reads. Transactions execute as if they were serialized (one after another). Lowest concurrency.
- **Locks**: Mechanisms to control concurrent access to data.
- **Deadlocks**: A situation where two or more transactions are waiting indefinitely for each other to release locks.

### Example Queries
```sql
-- Start a transaction
BEGIN TRANSACTION; -- or START TRANSACTION;

-- Perform operations within the transaction
UPDATE Accounts SET Balance = Balance - 100 WHERE AccountID = 1;
UPDATE Accounts SET Balance = Balance + 100 WHERE AccountID = 2;

-- Check if everything went well, then commit or rollback
-- If an error occurs:
-- ROLLBACK;
-- If successful:
COMMIT;

-- Setting Isolation Level (syntax varies)
-- SET TRANSACTION ISOLATION LEVEL READ COMMITTED; -- For SQL Server
-- SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED; -- For MySQL
```

### Best Practices/Pitfalls
- **Best Practice**: Keep transactions as short as possible to minimize lock contention and improve concurrency.
- **Best Practice**: Always handle error conditions by issuing a `ROLLBACK`.
- **Best Practice**: Choose the lowest isolation level that meets the application's data consistency requirements to maximize concurrency.
- **Pitfall**: Long-running transactions can lead to performance bottlenecks and increased chances of deadlocks.
- **Pitfall**: Not explicitly committing or rolling back transactions, leaving locks active and data in an inconsistent state.


## Views, Stored Procedures & Triggers
### Concept Summary
- `VIEWS`: A virtual table based on the result-set of an SQL query. A view contains rows and columns, just like a real table. The fields in a view are fields from one or more real tables in the database.
  - **Benefits**: Simplify complex queries, enhance security (limit access to specific columns/rows), provide data abstraction.
- `STORED PROCEDURES`: A prepared SQL code that you can save and reuse. They can accept input parameters and return output parameters.
  - **Benefits**: Encapsulation of logic, performance (pre-compiled), security, reduced network traffic.
- `TRIGGERS`: A special type of stored procedure that automatically executes or "fires" when a specific event occurs in the database (e.g., `INSERT`, `UPDATE`, `DELETE` on a table).
  - **Benefits**: Enforce complex business rules, audit changes, maintain data consistency across tables.

### Example Queries
```sql
-- Create a View
CREATE VIEW EmployeeSalaries AS
SELECT EmployeeID, FirstName, LastName, Salary
FROM Employees
WHERE Salary > 50000;

-- Querying a View
SELECT * FROM EmployeeSalaries WHERE LastName LIKE 'S%';

-- Create a Stored Procedure (Example for MySQL)
DELIMITER //
CREATE PROCEDURE GetEmployeesByDepartment (IN dept_id INT)
BEGIN
    SELECT FirstName, LastName, Salary
    FROM Employees
    WHERE DepartmentID = dept_id;
END //
DELIMITER ;

-- Execute a Stored Procedure
CALL GetEmployeesByDepartment(101);

-- Create a Trigger (Example for MySQL - Audit log on employee salary update)
DELIMITER //
CREATE TRIGGER AuditSalaryChange
BEFORE UPDATE ON Employees
FOR EACH ROW
BEGIN
    IF OLD.Salary <> NEW.Salary THEN
        INSERT INTO AuditLog (EmployeeID, OldSalary, NewSalary, ChangeDate)
        VALUES (OLD.EmployeeID, OLD.Salary, NEW.Salary, NOW());
    END IF;
END //
DELIMITER ;
```

### Best Practices/Pitfalls
- **Best Practice (Views)**: Use views to hide complex joins or sensitive data.
- **Best Practice (Procedures)**: Parameterize stored procedures to make them reusable and prevent SQL injection.
- **Best Practice (Triggers)**: Keep triggers short and focused. Avoid complex logic or long-running operations within triggers, as they run implicitly and can impact DML performance.
- **Pitfall (Views)**: Over-complex views can lead to poor query performance.
- **Pitfall (Procedures)**: Not handling errors properly within stored procedures.
- **Pitfall (Triggers)**: Triggers can make debugging difficult due to their implicit execution. Excessive triggers can lead to "trigger spaghetti."

## Advanced SQL Techniques
### Concept Summary
- **Common Table Expressions (CTEs)**: A temporary, named result set that you can reference within a single SQL statement (e.g., `SELECT`, `INSERT`, `UPDATE`, `DELETE`). Defined with the `WITH` clause.
  - **Benefits**: Improve readability for complex queries, break down complex logic into smaller, manageable parts, enable recursion.
- **Window Functions**: Perform calculations across a set of table rows that are related to the current row. Unlike aggregate functions, window functions do not collapse rows.
  - `ROW_NUMBER()`: Assigns a unique, sequential integer to each row within its partition, starting from 1.
  - `RANK()`: Assigns a rank to each row within its partition. Rows with the same value get the same rank, and the next rank is skipped.
  - `DENSE_RANK()`: Similar to `RANK()`, but does not skip ranks.
  - `LEAD(column, offset, default)`: Accesses a row at a given physical offset after the current row within its partition.
  - `LAG(column, offset, default)`: Accesses a row at a given physical offset before the current row within its partition.
- **Recursive Queries**: CTEs can be recursive, allowing a query to reference itself. Useful for hierarchical data (e.g., organizational charts, bill of materials).

### Example Queries
Assume `Employees` table with `EmployeeID`, `FirstName`, `LastName`, `Salary`, `DepartmentID`, `ManagerID`.

```sql
-- CTE (Find employees and their department's average salary)
WITH DepartmentAvgSalary AS (
    SELECT DepartmentID, AVG(Salary) AS AvgDeptSalary
    FROM Employees
    GROUP BY DepartmentID
)
SELECT E.FirstName, E.LastName, E.Salary, DAS.AvgDeptSalary
FROM Employees E
JOIN DepartmentAvgSalary DAS ON E.DepartmentID = DAS.DepartmentID
WHERE E.Salary > DAS.AvgDeptSalary;

-- Window Functions (Ranking employees within each department by salary)
SELECT
    EmployeeID,
    FirstName,
    LastName,
    DepartmentID,
    Salary,
    ROW_NUMBER() OVER (PARTITION BY DepartmentID ORDER BY Salary DESC) AS Rownum,
    RANK() OVER (PARTITION BY DepartmentID ORDER BY Salary DESC) AS RankNum,
    DENSE_RANK() OVER (PARTITION BY DepartmentID ORDER BY Salary DESC) AS DenseRankNum
FROM Employees;

-- LEAD and LAG (Calculate salary difference from previous and next employee by hire date)
SELECT
    EmployeeID,
    FirstName,
    HireDate,
    Salary,
    LAG(Salary, 1, 0) OVER (ORDER BY HireDate) AS PreviousSalary,
    LEAD(Salary, 1, 0) OVER (ORDER BY HireDate) AS NextSalary,
    Salary - LAG(Salary, 1, 0) OVER (ORDER BY HireDate) AS SalaryDifferenceFromPrev
FROM Employees;

-- Recursive CTE (Example: Find all subordinates for a given manager)
-- Assume Employees table has ManagerID (INT)
WITH Recursive Subordinates AS (
    -- Anchor member: The initial employee (e.g., the manager)
    SELECT EmployeeID, FirstName, LastName, ManagerID, 0 AS Level
    FROM Employees
    WHERE EmployeeID = 101 -- Starting manager's EmployeeID

    UNION ALL

    -- Recursive member: Find direct reports of the current level
    SELECT E.EmployeeID, E.FirstName, E.LastName, E.ManagerID, S.Level + 1
    FROM Employees E
    INNER JOIN Subordinates S ON E.ManagerID = S.EmployeeID
)
SELECT * FROM Subordinates;
```

### Best Practices/Pitfalls
- **Best Practice (CTEs)**: Use CTEs to make complex queries more readable and debuggable.
- **Best Practice (Window Functions)**: Understand the `PARTITION BY` and `ORDER BY` clauses within `OVER()`, as they define the window for calculation.
- **Pitfall (CTEs)**: While CTEs improve readability, they don't necessarily improve performance in all cases (sometimes subqueries are optimized better by the database).
- **Pitfall (Recursive CTEs)**: Be careful with infinite recursion. Ensure there's a termination condition.

## Database Design & Normalization
### Concept Summary
**Database Design**: The process of organizing data into tables and defining relationships between them to ensure data integrity, minimize redundancy, and optimize performance.
- **Normalization**: The process of organizing the columns and tables of a relational database to minimize data redundancy and improve data integrity. It involves breaking down a large table into smaller, related tables.
    - **1NF (First Normal Form)**:
      - Each column contains atomic (indivisible) values.
      - No repeating groups of columns.
      - Each row is unique.
    - **2NF (Second Normal Form)**:
      - Is in 1NF.
      - All non-key attributes are fully functionally dependent on the primary key (no partial dependencies).
    - **3NF (Third Normal Form)**:
      - Is in 2NF.
      - No transitive dependencies (non-key attributes are not dependent on other non-key attributes).
    - **BCNF (Boyce-Codd Normal Form)**: A stricter version of 3NF. Every determinant must be a candidate key.
- **Denormalization**: Intentionally introducing redundancy into a database schema, typically to improve read performance for specific queries, often at the cost of increased write complexity and potential data inconsistency.
- **Entity-Relationship (ER) Modeling**: A high-level data model that describes the entities (things of interest), their attributes (properties), and the relationships between them. Represented using ER diagrams.

### Example (Conceptual)
- **Unnormalized table**: `Orders (OrderID, CustomerName, CustomerAddress, Product1Name, Product1Price, Product2Name, Product2Price)` - violates 1NF (repeating groups).
- **1NF**: `Orders (OrderID, CustomerName, CustomerAddress, ProductNo, ProductName, ProductPrice)` - still has partial dependency (CustomerName/Address depend only on OrderID, not (OrderID, ProductNo)).
- **2NF**: `Orders (OrderID, CustomerID, OrderDate), Customers (CustomerID, CustomerName, CustomerAddress), OrderDetails (OrderID, ProductID, Quantity, PriceAtOrder), Products (ProductID, ProductName, UnitPrice)` - now, ProductPrice in OrderDetails is an issue (transitive dependency if PriceAtOrder is supposed to be latest from Products).
- **3NF**: Ensures no transitive dependencies.

### Best Practices/Pitfalls
- **Best Practice**: Aim for 3NF or BCNF in transactional systems to ensure data integrity and reduce redundancy.
- **Best Practice**: Understand the business requirements before deciding on the level of normalization.
- **Pitfall**: Over-normalizing can lead to excessive joins and degrade read performance.
- **Pitfall**: Under-normalizing leads to data redundancy, update anomalies, and inconsistent data.

## Query Optimization
### Concept Summary
Query optimization is the process of improving the performance of SQL queries to reduce execution time and resource consumption.
- **Execution Plans**: A graphical or textual representation of the steps the database query optimizer will take to execute an SQL query. It shows how tables are accessed (index scan, table scan), how they are joined, and how data is filtered and aggregated. Crucial for identifying performance bottlenecks.
- **Index Usage Analysis**: Analyzing how indexes are being used (or not used) by the query optimizer.
- **Query Refactoring Techniques**: Modifying the SQL query to improve its performance without changing its logical outcome. This often involves:
  - Using appropriate joins instead of subqueries where beneficial.
    - Avoiding SELECT *.
  - Filtering early in the query.
  - Using `EXISTS` instead of `IN` for certain scenarios.
  - Optimizing `LIKE` clauses (e.g., avoiding leading wildcards).
  - Avoiding functions on indexed columns in `WHERE` clauses.

### Example (Conceptual)
- **Bad Query**: `SELECT * FROM Orders WHERE YEAR(OrderDate) = 2024;` (Function on indexed column OrderDate prevents index usage).
- **Optimized Query**: `SELECT * FROM Orders WHERE OrderDate >= '2024-01-01' AND OrderDate < '2025-01-01';` (Allows index usage on `OrderDate`).

### Best Practices/Pitfalls
- **Best Practice**: Always check the execution plan for slow queries.
- **Best Practice**: Use `EXPLAIN` (MySQL/PostgreSQL) or `SET SHOWPLAN_ALL` (SQL Server) to view execution plans.
- **Best Practice**: Keep an eye on `WHERE` clauses – they are the primary candidates for indexing.
- **Pitfall**: Assuming that adding an index will always improve performance (it can sometimes degrade it).
- **Pitfall**: Blindly rewriting queries without understanding the database's optimizer behavior

## Real-World Scenarios & Case Studies
### Concept Summary
This section is about applying your SQL knowledge to practical business problems. It often involves combining multiple concepts learned previously.
- **Reporting Queries**: Extracting data for business reports (e.g., sales reports, employee performance, financial summaries). Often involves aggregation, grouping, and sometimes window functions.
- **Analytics Queries**: Deeper dives into data to identify trends, patterns, and insights (e.g., monthly user activity, customer churn analysis, product performance). May involve time-series analysis, calculating percentages, and complex filtering.
- **Data Migration Scripts**: SQL scripts used to move data between different databases or transform data during system upgrades or integrations. Requires careful planning for data integrity and error handling.

### Example Queries (Illustrative, building on previous concepts)
#### Scenario 1: Top 5 Best-Selling Products by Revenue (Last Month)
Assume `Orders (OrderID, OrderDate, CustomerID)`, `OrderItems (OrderItemID, OrderID, ProductID, Quantity, PriceEach)`, `Products (ProductID, ProductName)`

```sql
SELECT
    P.ProductName,
    SUM(OI.Quantity * OI.PriceEach) AS TotalRevenue
FROM
    Orders O
JOIN
    OrderItems OI ON O.OrderID = OI.OrderID
JOIN
    Products P ON OI.ProductID = P.ProductID
WHERE
    O.OrderDate >= '2025-05-01' AND O.OrderDate < '2025-06-01' -- For May 2025
GROUP BY
    P.ProductName
ORDER BY
    TotalRevenue DESC
LIMIT 5;
```

#### Scenario 2: Monthly Active Users (Hypothetical `UserActivity` table with `UserID`, `ActivityDate`)
```sql
SELECT
    DATE_FORMAT(ActivityDate, '%Y-%m') AS ActivityMonth,
    COUNT(DISTINCT UserID) AS MonthlyActiveUsers
FROM
    UserActivity
GROUP BY
    ActivityMonth
ORDER BY
    ActivityMonth;

-- For Month-over-Month Growth (using CTE and LAG)
WITH MonthlyUsers AS (
    SELECT
        DATE_FORMAT(ActivityDate, '%Y-%m') AS ActivityMonth,
        COUNT(DISTINCT UserID) AS MonthlyActiveUsers
    FROM
        UserActivity
    GROUP BY
        ActivityMonth
)
SELECT
    M1.ActivityMonth,
    M1.MonthlyActiveUsers,
    LAG(M1.MonthlyActiveUsers, 1, 0) OVER (ORDER BY M1.ActivityMonth) AS PreviousMonthUsers,
    (M1.MonthlyActiveUsers - LAG(M1.MonthlyActiveUsers, 1, 0) OVER (ORDER BY M1.ActivityMonth)) * 100.0 / LAG(M1.MonthlyActiveUsers, 1, 1) OVER (ORDER BY M1.ActivityMonth) AS MoMGrowthPercentage
FROM
    MonthlyUsers M1
ORDER BY
    M1.ActivityMonth;
```

#### Best Practices/Pitfalls
- **Best Practice**: Always clarify requirements for real-world scenarios. Ask about data volume, performance expectations, and specific business rules.
- **Best Practice**: Break down complex problems into smaller, manageable sub-queries or CTEs.
- **Best Practice**: Consider edge cases and how your query would handle them (e.g., what if there's no data for a given period?).
- **Pitfall**: Jumping into writing complex queries without a clear understanding of the data model and the problem.
- **Pitfall**: Not testing your queries with realistic data volumes.