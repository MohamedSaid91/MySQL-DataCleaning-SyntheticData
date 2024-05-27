# MySQL-DataCleaning-SyntheticData

This project involves data cleaning in MySQL, addressing issues like missing values, invalid formats, duplicates, and inconsistencies in a sample employees dataset to ensure data accuracy and integrity.

### Step 1: Create the Dataset

-- Creating a sample dataset with different types of data issues:

CREATE DATABASE IF NOT EXISTS data_cleaning_project;
USE data_cleaning_project;

-- Drop the table if it already exists
DROP TABLE IF EXISTS employees;

-- Create the employees table
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100),
    hire_date DATE,
    salary DECIMAL(10, 2),
    department_id INT,
    manager_id INT,
    comments TEXT
);

-- Insert sample data with various data issues
INSERT INTO employees (employee_id, first_name, last_name, email, hire_date, salary, department_id, manager_id, comments) VALUES
(1, 'John', 'Doe', 'john.doe@example.com', '2023-01-15', 60000, 101, 5, NULL),
(2, 'Jane', 'Smith', 'jane.smith@example.com', '2022-02-20', 72000, NULL, 5, 'Promoted recently.'),
(3, 'Bob', NULL, 'bob@example.com', '2023-03-25', 55000, 102, NULL, 'Temporary employee.'),
(4, 'Alice', 'Johnson', 'alice.johnson@exampl', '2021-12-05', -45000, 103, 2, 'Salary adjustment needed.'),
(5, 'Charles', 'Brown', 'charles.brown@example.com', '2022-11-15', 90000, 101, NULL, 'Pending background check.'),
(6, 'Eva', 'Green', 'eva.green@example.com', 'invalid_date', 80000, 104, 5, 'Hire date needs correction.'),
(7, NULL, 'White', 'white@example.com', '2023-05-01', 70000, 105, 6, 'Name correction required.'),
(8, 'George', 'Black', 'george.black@exmaple.com', '2021-07-19', 68000, 106, 1, 'Email typo.'),
(9, 'Diana', 'Blue', 'diana.blue@example.com', '2022-04-21', NULL, 107, 3, 'Missing salary information.'),
(10, 'Chris', 'Red', 'chris.red@example.com', '2023-06-30', 75000, 108, 4, 'All details are correct.');


### Step 2: Data Cleaning Queries

#### 1. Remove Invalid Email Addresses

-- Fix invalid email addresses
UPDATE employees
SET email = REPLACE(email, 'exampl', 'example')
WHERE email LIKE '%@exampl.com%';


#### 2. Handle Missing Values

-- Handle missing first names
UPDATE employees
SET first_name = 'Unknown'
WHERE first_name IS NULL;

-- Handle missing last names
UPDATE employees
SET last_name = 'Unknown'
WHERE last_name IS NULL;

-- Handle missing department_id with default value
UPDATE employees
SET department_id = 999 -- Assuming 999 is the default for unknown departments
WHERE department_id IS NULL;

-- Handle missing salary with average salary
UPDATE employees
SET salary = (SELECT AVG(salary) FROM employees WHERE salary IS NOT NULL)
WHERE salary IS NULL;


#### 3. Correct Negative Salary Values

-- Fix negative salary values
UPDATE employees
SET salary = ABS(salary)
WHERE salary < 0;

#### 4. Validate and Correct Date Formats

-- Correct invalid date formats
UPDATE employees
SET hire_date = '2023-01-01' -- Defaulting to a valid date
WHERE hire_date = 'invalid_date';

#### 5. Remove Duplicates

-- Remove duplicate records based on email
DELETE e1
FROM employees e1
INNER JOIN employees e2 
WHERE e1.employee_id > e2.employee_id
AND e1.email = e2.email;


#### 6. Normalize Data (e.g., Titles in Comments)

-- Normalize comments to have consistent titles
UPDATE employees
SET comments = REPLACE(comments, 'Promoted', 'Promotion')
WHERE comments LIKE '%Promoted%';


#### 7. Standardize Names (Capitalization)

-- Standardize first names to capitalize first letter
UPDATE employees
SET first_name = CONCAT(UCASE(LEFT(first_name, 1)), LCASE(SUBSTRING(first_name, 2)));

-- Standardize last names to capitalize first letter
UPDATE employees
SET last_name = CONCAT(UCASE(LEFT(last_name, 1)), LCASE(SUBSTRING(last_name, 2)));

#### 8. Remove Unnecessary White Spaces

-- Remove unnecessary white spaces in names
UPDATE employees
SET first_name = TRIM(first_name),
    last_name = TRIM(last_name);

### Step 3: Validate Cleaned Data

-- To ensure that the data cleaning process was successful, run a query to validate the cleaned data:

SELECT * FROM employees;
