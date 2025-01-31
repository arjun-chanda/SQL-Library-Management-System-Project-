# SQL-Library-Management-System-Project-
## Project Overview

**Project Title**: Library Management System  
**Level**: Intermediate  
**Database**: ` LibraryManagement`

This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations, and executing advanced SQL queries. The goal is to showcase skills in database design, manipulation, and querying.

![Library Management Project](https://github.com/user-attachments/assets/c7bea649-bdf7-482d-8e82-6a33c3b1aa50)

## Objectives

1. **Set up the Library Management System Database**: Create and populate the database with tables for branches, employees, members, books, issued status, and return status.
2. **CRUD Operations**: Perform Create, Read, Update, and Delete operations on the data.
3. **CTAS (Create Table As Select)**: Utilize CTAS to create new tables based on query results.
4. **Advanced SQL Queries**: Develop complex queries to analyze and retrieve specific data.

## Project Structure

### 1. Database Setup
![ERD DIAGRAM](https://github.com/user-attachments/assets/cc20d57d-fd5a-4e8a-b09d-2c6aa2c95d69)

- **Database Creation**: Created a database named `LibraryManagement`.
- **Table Creation**: Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.
- ```sql

  CREATE DATABASE LibraryManagement;
  USE LibraryManagement;
  
  -- creating table for branch--
  CREATE TABLE Branch 
  ( 
  branch_id VARCHAR(10) PRIMARY KEY ,
  manager_id VARCHAR(10),  	
  branch_address VARCHAR(50),  
  contact_no VARCHAR(10)
  );

  ALTER TABLE Branch
  MODIFY COLUMN contact_no  VARCHAR(15);

  -- creating table for Employee--

  CREATE TABLE Employees
  (
  emp_id VARCHAR(10) PRIMARY KEY,
  emp_name VARCHAR(25),
  position VARCHAR(15),
  salary FLOAT,
  branch_id VARCHAR(10) -- FK--
  );
  
  ALTER TABLE Employees
  MODIFY COLUMN  emp_name  VARCHAR(30),
  MODIFY COLUMN position  VARCHAR(30),
  MODIFY COLUMN salary DECIMAL(10,2);

  -- creating table for Books--
    CREATE TABLE Books
  ( 
   isbn VARCHAR(50) PRIMARY KEY ,
   book_title VARCHAR(90),
   category VARCHAR(20),
   rental_price DECIMAL(10,2),
   status VARCHAR(10),
   author VARCHAR(90),
   publisher VARCHAR(50)
   );
   
    -- creating table for members --
     CREATE TABLE Members
     (
       member_id VARCHAR(15) PRIMARY KEY ,
       member_name VARCHAR(50),
       member_address VARCHAR(95),
       reg_date DATE
       );
       
	-- creating table for issued_status --
    CREATE TABLE Issuedstatus
    (
    issued_id VARCHAR(20) PRIMARY KEY ,
	issued_member_id VARCHAR(15), -- fk --
	issued_book_name VARCHAR(75),
	issued_date DATE,
	issued_book_isbn VARCHAR(90), -- fk --
	issued_emp_id VARCHAR(25) -- fk -- 
    );
    
  ALTER TABLE Issuedstatus
  MODIFY COLUMN  issued_member_id VARCHAR(30),
  MODIFY COLUMN  issued_book_name VARCHAR(80);
      
    -- creating table for return_status --
    CREATE TABLE return_status
    ( 
    return_id VARCHAR(15) PRIMARY KEY ,
	issued_id VARCHAR(15), -- fk --
	return_book_name VARCHAR(90),
	return_date DATE,
	return_book_isbn VARCHAR(90)
    );
  ALTER TABLE return_status
  MODIFY COLUMN issued_id VARCHAR(30);

  SHOW tables;
  
  -- FOREIGN KEY--


  ALTER TABLE  Issuedstatus
  ADD CONSTRAINT fk_members
  FOREIGN KEY ( issued_member_id)
  REFERENCES members( member_id);

  ALTER TABLE  Issuedstatus
  ADD CONSTRAINT fk_books
  FOREIGN KEY ( issued_book_isbn)
  REFERENCES books( isbn);

  ALTER TABLE  Employees
  ADD CONSTRAINT fk_branch
  FOREIGN KEY (   branch_id )
  REFERENCES  Branch (branch_id);

  ALTER TABLE  return_status
  ADD CONSTRAINT fk_issued_status
  FOREIGN KEY ( issued_id)
  REFERENCES Issuedstatus(  issued_id);

  - CRUD Operations --
  
  - **Create**: Inserted sample records into the `books` table.
  - **Read**: Retrieved and displayed data from various tables.
  - **Update**: Updated records in the `employees` table.
  - **Delete**: Removed records from the `members` table as needed.

  ** Task 1. Create a New Book Record -- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')" ** 


  INSERT INTO books (isbn,book_title,category,rental_price,status,author,publisher)
  VALUES('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co');
  SELECT * FROM books;
  --
  ** Task 2: Update an Existing Member's Address **

  UPDATE members
  SET member_address = '125 Oak St'
  where member_id=  'C103';
  SELECT * FROM members;

  --** Task 3: Delete a Record from the Issued Status Table -- Objective: Delete the record with issued_id = 'IS121' from the issued_status table. **

  DELETE FROM Issuedstatus
  WHERE issued_id ='IS121';

  -- ** Task 4: Retrieve All Books Issued by a Specific Employee -- Objective: Select all books issued by the employee with emp_id = 'E101'. **

  SELECT * FROM Issuedstatus
  WHERE issued_emp_id= 'E101';


  -- ** Task 5: List Members Who Have Issued More Than One Book -- Objective: Use GROUP BY to find members who have issued more than one book. **
  
  SELECT
  issued_emp_id,
  COUNT(*)
  FROM Issuedstatus
  GROUP BY 1
  HAVING COUNT(*) > 1;
  
  -- ### 3. CTAS (Create Table As Select)
  
  -- **Task 6: Create Summary Tables**: Used CTAS to generate new tables based on query results - each book and total book_issued_cnt **

  CREATE TABLE book_issued_cnt AS
  SELECT 
  b.isbn,
  b.book_title,
  COUNT(ist.issued_id) as is_num
  FROM books AS b
  JOIN 
  Issuedstatus AS ist 
  ON ist.issued_book_isbn = b.isbn
   GROUP BY 1,2;
 
  SELECT * FROM book_issued_cnt;

  -- ### 4. Data Analysis & Findings 

  -- **Task 7. **Retrieve All Books in a Specific Category: **

  SELECT * FROM books
  WHERE category = 'Classic';

  -- **Task 8: Find Total Rental Income by Category: **

  SELECT * FROM books;
     
  SELECT 
  b.category,
  SUM(b.rental_price),
  COUNT(*)
  FROM books AS b
  JOIN 
  Issuedstatus AS ist 
  ON ist.issued_book_isbn = b.isbn
  GROUP BY 1;
     
  SELECT * FROM books;

  -- **Task 9. **List Members Who Registered in the Last 180 Days**: **


  INSERT INTO members (member_id,member_name,member_address,reg_date)
  VALUES
  ('c125','ram','126 Main st','2024-06-01'),
  ('c130','krish','156 Main st','2024-05-01');
    
  SELECT * FROM members
  WHERE reg_date >= CURRENT_DATE-INTERVAl 180 DAY ;

  -- **Task 10: List Employees with Their Branch Manager's Name and their branch details**: **

  SELECT  
  e1.*,
  b.manager_id,
  e2.emp_name AS manager 
  FROM employees AS e1
  JOIN
  branch AS b
  ON b.branch_id=e1.branch_id
  JOIN
  employees AS e2
  ON b.manager_id=e2.emp_id;
     
  -- **Task 11. Create a Table of Books with Rental Price Above a Certain Threshold: **
 
  CREATE TABLE expensive_books AS 
  SELECT * FROM books
  WHERE rental_price >= 7.00;

  -- ** Task 12: Retrieve the List of Books Not Yet Returned**
  
  SELECT * FROM Issuedstatus AS ist
  LEFT JOIN
  return_status AS rs
  ON ist.issued_id = rs.issued_id
  WHERE  rs.return_id is NULL ;

  -- -- Inserting some additional data --
  
  INSERT INTO Issuedstatus (issued_id, issued_member_id, issued_book_name, issued_date, issued_book_isbn, issued_emp_id)
  VALUES
  ('IS151', 'C118', 'The Catcher in the Rye', CURRENT_DATE - INTERVAL 24 DAY, '978-0-553-29698-2', 'E108'),
  ('IS152', 'C119', 'The Catcher in the Rye', CURRENT_DATE - INTERVAL 13 DAY, '978-0-553-29698-2', 'E109'),
  ('IS153', 'C106', 'Pride and Prejudice', CURRENT_DATE - INTERVAL 7 DAY, '978-0-14-143951-8', 'E107'),
  ('IS154', 'C105', 'The Road', CURRENT_DATE - INTERVAL 32 DAY, '978-0-375-50167-0', 'E101');


  -- ADDING coloum in returnstatus --
  ALTER TABLE return_status
  ADD Column book_quality VARCHAR(15) DEFAULT('Good');

  UPDATE return_status
  SET book_quality = 'Damaged'
  WHERE issued_id 
  IN ('IS112', 'IS117', 'IS118');
    
  -- Advanced SQL Operations --
  **/* Task 13: Identify Members with Overdue Books
  Write a query to identify members who have overdue books (assume a 30-day return period). 
  Display the member's_id, member's name, book title, issue date, and days overdue. */ **
 
  SELECT 
  ist.issued_member_id,
  m.member_name,
  bk.book_title,
  ist.issued_date,
  CURRENT_DATE - ist.issued_date AS over_dues
  FROM Issuedstatus AS ist
  JOIN
  members AS m
  ON m.member_id = issued_member_id
  JOIN
  books AS bk
  ON bk.isbn = ist.issued_book_isbn
  LEFT JOIN 
  return_status AS rs
  ON rs.issued_id = ist.issued_id
  WHERE rs.return_date IS NULL
  AND
  ( CURRENT_DATE - ist.issued_date) > 30
  order BY 1;

  **/*Task 14: Branch Performance Report
  Create a query that generates a performance report for each branch,
  showing the number of books issued, the number of books returned, and the total revenue 
  generated from book rentals.*/**

  CREATE TABLE branch_reports
  AS
  SELECT 
  b.branch_id,
  b.manager_id,
  COUNT(ist.issued_id) as number_book_issued,
  COUNT(rs.return_id) as number_of_book_return,
  SUM(bk.rental_price) as total_revenue
  FROM Issuedstatus as ist
  JOIN 
  employees as e
  ON e.emp_id = ist.issued_emp_id
  JOIN
  branch as b
  ON e.branch_id = b.branch_id
  LEFT JOIN
  return_status as rs
  ON rs.issued_id = ist.issued_id
  JOIN 
  books as bk
  ON ist.issued_book_isbn = bk.isbn
  GROUP BY 1, 2;
    
  SELECT * FROM branch_reports;


  **/*Task 15: CTAS: Create a Table of Active Members
  Use the CREATE TABLE AS (CTAS) statement to create a new table active_members 
  containing members who have issued at least one book in the last 2 months.*/**


  CREATE TABLE active_members AS
  SELECT * FROM members
  WHERE member_id IN (
  SELECT DISTINCT issued_member_id   
        
  FROM Issuedstatus
  WHERE issued_date >= CURRENT_DATE - INTERVAL 2 MONTH
  );

    
## Reports

- **Database Schema**: Detailed table structures and relationships.
- **Data Analysis**: Insights into book categories, employee salaries, member registration trends, and issued books.
- **Summary Reports**: Aggregated data on high-demand books and employee performance.

## Conclusion

This project demonstrates the application of SQL skills in creating and managing a library management system. It includes database setup, data manipulation, and advanced querying, providing a solid foundation for data management and analysis.

## How to Use

1. **Clone the Repository**: Clone this repository to your local machine.
   ```sh
   https://github.com/arjun-chanda/SQL-Library-Management-System-Project-.git
   ```

2. **Set Up the Database**: Execute the SQL scripts in the `database_setup.sql` file to create and populate the database.
3. **Run the Queries**: Use the SQL queries in the `analysis_queries.sql` file to perform the analysis.
4. **Explore and Modify**: Customize the queries as needed to explore different aspects of the data or answer additional questions.

## Author - Arjun Chanda 



