# SQL

![alt text](https://www.w3schools.com/sql/img_rightjoin.gif) 

select * from auth_user limit 10;	=> show first 10 records
select * from auth_user limit 10,10;	=> show next 10 records i.e. from 10 to next 10 records
or
select * from auth_user limit 10 offset 10;
select * from auth_user order by id desc limit 10;	=> show last 10 records
SELECT * FROM Customers ORDER BY Country DESC;     => sorted DESCENDING by the "Country" column
SELECT * FROM Customers ORDER BY Country ASC, CustomerName DESC; => sorted DESCENDING by the "Country" and ASCENDING by CustomerName
INSERT INTO Customers (CustomerName, City, Country) VALUES ('Cardinal', 'Stavanger', 'Norway');
UPDATE Customers SET ContactName='Juan' WHERE Country='Mexico';
DELETE FROM Customers WHERE CustomerName='Alfreds Futterkiste'; => deletes the customer "Alfreds Futterkiste" from the "Customers" table
DELETE FROM Customers; => deletes all rows in the "Customers" table, without deleting the table

############## selects the first three records from the "Customers" table ##################
SELECT TOP 3 * FROM Customers;
SELECT * FROM Customers LIMIT 3;
SELECT * FROM Customers WHERE ROWNUM <= 3;
##################### the above three are the same ##########################################
SELECT TOP 50 PERCENT * FROM Customers; => selects the first 50% of the records from the "Customers" table
SELECT MIN(Price) AS SmallestPrice FROM Products;         => finds the price of the cheapest product
SELECT MAX(Price) AS LargestPrice FROM Products;          => finds the price of the most expensive product

SELECT COUNT(ProductID) FROM Products;   =>   finds the number of products
SELECT AVG(Price) FROM Products;         =>   finds the average price of all products
SELECT SUM(Quantity) FROM OrderDetails;  =>   finds the sum of the "Quantity" fields in the "OrderDetails" table

# % - The percent sign represents zero, one, or multiple characters
# _ - The underscore represents a single character

SELECT * FROM Customers WHERE CustomerName LIKE 'a%'	   => Finds any values that start with "a"
SELECT * FROM Customers WHERE CustomerName LIKE '%a'	   => Finds any values that end with "a"
SELECT * FROM Customers WHERE CustomerName LIKE '%es%'	 => Finds any values that have "es" in any position
SELECT * FROM Customers WHERE CustomerName LIKE '_r%'	  =>Finds any values that have "r" in the second position
SELECT * FROM Customers WHERE CustomerName LIKE 'a_%_%'	=>Finds any values that start with "a" and are at least 3 characters in length
SELECT * FROM Customers WHERE ContactName LIKE 'a%o'	   => Finds any values that start with "a" and ends with "o"
SELECT * FROM Customers WHERE City LIKE '[bsp]%';       => selects all customers with a City starting with "b", "s", or "p"
SELECT * FROM Customers WHERE City LIKE '[a-c]%';       => selects all customers with a City starting with "a", "b", or "c"
SELECT * FROM Customers WHERE City LIKE '[!bsp]%';      =>  select all customers with a City NOT starting with "b", "s", or "p"
SELECT * FROM Customers WHERE City NOT LIKE '[bsp]%';   => City NOT starting with "b", "s", or "p"

SELECT * FROM Customers WHERE Country NOT IN ('Germany', 'France', 'UK'); => selects all customers that are NOT located in "Germany", "France" or "UK"
SELECT * FROM Customers WHERE Country IN (SELECT Country FROM Suppliers);

SELECT * FROM Products WHERE Price BETWEEN 10 AND 20;
SELECT * FROM Products WHERE Price NOT BETWEEN 10 AND 20;

# selects all products with a ProductName BETWEEN 'Carnarvon Tigers' and 'Mozzarella di Giovanni'
SELECT * FROM Products WHERE ProductName BETWEEN 'Carnarvon Tigers' AND 'Mozzarella di Giovanni' ORDER BY ProductName;

############## selects all orders with an OrderDate BETWEEN '01-July-1996' and '31-July-1996' ##############
SELECT * FROM Orders WHERE OrderDate BETWEEN #01/07/1996# AND #31/07/1996#;
SELECT * FROM Orders WHERE OrderDate BETWEEN '1996-07-01' AND '1996-07-31';
############## both of the above are same #######################

SELECT CustomerID AS ID, CustomerName AS Customer FROM Customers;                   => creating alias
SELECT CustomerName AS Customer, ContactName AS [Contact Person] FROM Customers;    => creates two aliases
SELECT CustomerName, CONCAT(Address,', ',PostalCode,', ',City,', ',Country) AS Address FROM Customers; => combine four columns


SELECT DISTINCT Country FROM Customers;            => selects only the DISTINCT values
SELECT * FROM Customers WHERE Country='Germany' AND City='Berlin';
SELECT * FROM Customers WHERE Country='Germany' OR City='Berlin';
SELECT CustomerName, ContactName, Address FROM Customers WHERE Address IS NOT NULL;

 
DDL(Data Definition Language) statements are used to define the database structure or schema. Some examples:
o	CREATE - to create objects in the database
o	ALTER - alters the structure of the database
o	DROP - delete objects from the database
o	TRUNCATE - remove all records from a table, including all spaces allocated for the records are removed
o	COMMENT - add comments to the data dictionary
o	RENAME - rename an object
DML(Data Manipulation Language) statements are used for managing data within schema objects. Some examples:
o	SELECT - retrieve data from the a database
o	INSERT - insert data into a table
o	UPDATE - updates existing data within a table
o	DELETE - deletes all records from a table, the space for the records remain
o	MERGE - UPSERT operation (insert or update)
o	CALL - call a PL/SQL or Java subprogram
o	EXPLAIN PLAN - explain access path to data
o	LOCK TABLE - control concurrency
DCL(Data Control Language) statements. Some examples:
o	GRANT - gives user's access privileges to database
o	REVOKE - withdraw access privileges given with the GRANT command
TCL(Transaction Control) statements are used to manage the changes made by DML statements. It allows statements to be grouped together into logical transactions.
o	COMMIT - save work done
o	SAVEPOINT - identify a point in a transaction to which you can later roll back
o	ROLLBACK - restore database to original since the last COMMIT
SET TRANSACTION - Change transaction options like isolation level and what rollback segment to use

Show tables which are not empty: SHOW TABLE STATUS WHERE Rows > 0;
Truncate all tables in a database:
mysql -u root -proot123 -Nse 'show tables' DATABASE_NAME | while read table; do mysql -u root -proot123 -e "truncate table $table" DATABASE_NAME; done
e.g.
mysql -u root -proot123 -Nse 'show tables' XE | while read table; do mysql -u root -proot123 -e "truncate table $table" XE; done

Bash script:
mysql -u root -proot123 -Nse 'show tables' XE | while read table; do mysql -u root -proot123 -e "truncate table $table" XE; done
if [ $? = 0 ]; then
echo "SUCCESS. All tables are truncated."
Fi


select concat('rename table ', table_name, ' to ' , lower(table_name) , ';') from information_schema.tables;
SQL CREATE INDEX Statement
The CREATE INDEX statement is used to create indexes in tables.
Indexes are used to retrieve data from the database very fast. The users cannot see the indexes, they are just used to speed up searches/queries.
Note: Updating a table with indexes takes more time than updating a table without (because the indexes also need an update). So, only create indexes on columns that will be frequently searched against.
CREATE INDEX Syntax
Creates an index on a table. Duplicate values are allowed:
CREATE INDEX index_name
ON table_name (column1, column2, ...); 
CREATE UNIQUE INDEX Syntax
Creates a unique index on a table. Duplicate values are not allowed:
CREATE UNIQUE INDEX index_name
ON table_name (column1, column2, ...); 
Note: The syntax for creating indexes varies among different databases. Therefore: Check the syntax for creating indexes in your database.
________________________________________
CREATE INDEX Example
The SQL statement below creates an index named "idx_lastname" on the "LastName" column in the "Persons" table:
CREATE INDEX idx_lastname
ON Persons (LastName); 
If you want to create an index on a combination of columns, you can list the column names within the parentheses, separated by commas:
CREATE INDEX idx_pname
ON Persons (LastName, FirstName); 
________________________________________
DROP INDEX Statement
The DROP INDEX statement is used to delete an index in a table.
MS Access:
DROP INDEX index_name ON table_name; 
SQL Server:
DROP INDEX table_name.index_name; 
DB2/Oracle:
DROP INDEX index_name; 
MySQL:
ALTER TABLE table_name
DROP INDEX index_name; 

MySQL does not support sequences like CREATE SEQUENCE in ORACLE. The simplest way in MySQL to use Sequences is to define a column as AUTO_INCREMENT and leave the remaining things to MySQL to take care.
e.g. 
CREATE TABLE ORD (
  ORDID INT NOT NULL AUTO_INCREMENT,
  //Rest of table code
  PRIMARY KEY (ordid)
) AUTO_INCREMENT = 622;

OR you can use it later by altering the table like below.
ALTER TABLE ORD AUTO_INCREMENT = 622;
mysqldump -u [username] –p[password] [database_name] > [dumpfilename.sql]

 
INNER JOIN gets all records that are common between both tables
LEFT JOIN gets all records from the LEFT linked table but if you have selected some columns from the RIGHT table, if there is no related records, these columns will contain NULL
RIGHT JOIN is like the above but gets all records in the RIGHT table
FULL JOIN gets all records from both tables and puts NULL in the columns where related records do not exist in the opposite table
SELF JOIN: is used to join a table to itself as if the table were two tables, temporarily renaming at least one table in the SQL statement.
CARTESIAN JOIN: returns the Cartesian product of the sets of records from the two or more joined tables.
We have two tables with the following values.
TableA
id  firstName                  lastName
.......................................
1   arun                        prasanth                 
2   ann                         antony                   
3   sruthy                      abc                      
6   new                         abc                                           

TableB
id2 age Place
................
1   24  kerala
2   24  usa
3   25  ekm
5   24  chennai
....................................................................
INNER JOIN
Note :it gives the intersection of the two tables, i.e. rows they have common in TableA and TableB
Syntax
SELECT table1.column1, table2.column2...
FROM table1
INNER JOIN table2
ON table1.common_field = table2.common_field;

Apply it in our sample table: 
SELECT TableA.firstName,TableA.lastName,TableB.age,TableB.Place
FROM TableA
INNER JOIN TableB
ON TableA.id = TableB.id2;

Result Will Be 
firstName       lastName       age  Place
..............................................
arun            prasanth        24  kerala
ann             antony          24  usa	
sruthy          abc             25  ekm

LEFT JOIN
Note : will give all selected rows in TableA, plus any common selected rows in TableB. 
* Not all rows from TableB

Syntax 
SELECT table1.column1, table2.column2...
FROM table1
LEFT JOIN table2
ON table1.common_field = table2.common_field;

Apply it in our sample table :
SELECT TableA.firstName,TableA.lastName,TableB.age,TableB.Place
FROM TableA
LEFT JOIN TableB
ON TableA.id = TableB.id2;

Result
firstName                   lastName                    age   Place
...............................................................................
arun                        prasanth                    24    kerala
ann                         antony                      24    usa
sruthy                      abc                         25    ekm
new                         abc                         NULL  NULL

RIGHT JOIN
Note: will give all selected rows in TableB, plus any common selected rows in TableA. 
* Not all rows from TableA

Syntax 
SELECT table1.column1, table2.column2...
FROM table1
RIGHT JOIN table2
ON table1.common_field = table2.common_field;

Apply it in our sample table :
SELECT TableA.firstName,TableA.lastName,TableB.age,TableB.Place
FROM TableA
RIGHT JOIN TableB
ON TableA.id = TableB.id2;

Result 
firstName                   lastName                    age     Place
...............................................................................
arun                        prasanth                    24     kerala
ann                         antony                      24     usa
sruthy                      abc                         25     ekm
NULL                        NULL                        24     chennai


FULL JOIN
Note : It is same as union operation, it will return all selected values from both tables.
Syntax 
SELECT table1.column1, table2.column2...
FROM table1
FULL JOIN table2
ON table1.common_field = table2.common_field;

Apply it in our sample table :
SELECT TableA.firstName,TableA.lastName,TableB.age,TableB.Place
FROM TableA
FULL JOIN TableB
ON TableA.id = TableB.id2;

Result 
firstName                   lastName                    age    Place
...............................................................................
arun                        prasanth                    24    kerala
ann                         antony                      24    usa
sruthy                      abc                         25    ekm
new                         abc                         NULL  NULL
NULL                        NULL                        24    chennai

Note: For INNER joins the order doesn't matter
For (LEFT, RIGHT or FULL) OUTER joins,the order matter
Ref: https://stackoverflow.com/questions/5706437/whats-the-difference-between-inner-join-left-join-right-join-and-full-join
Difference between view and stored procedures and Triggers:
Stored Procedure
•	Stored Procedure can have multiple select statement
•	Stored Procedure exists in database
•	Stored procedure return predefined result
•	Stored Procedure does data modification
•	Accept parameters. It’s a collection of pre-executed sql Statements where you can send the parameters as input and retrieve the output data.
•	
•	can NOT be used as building block in a larger query 
•	can contain several statements, loops, IF ELSE, etc. 
•	can perform modifications to one or several tables 
•	can NOT be used as the target of an INSERT, UPDATE or DELETE statement. 


View
•	View can have only one select statement
•	View are like virtual tables and does not exists in database
•	Views can return different result based on where condition
•	View cannot modify data and DML is forbidden when you define the view
•	Doesn’t accept parameter
•	can be used as building block in a larger query 
•	can contain only one single SELECT query 
•	can NOT perform modifications to any table 
•	
but can (sometimes) be used as the target of an INSERT, UPDATE or DELETE statement.

Also Views shouldn't contain any "order by" or "top" clauses
Summary: Stored procedures are best used for INSERT-UPDATE-DELETE statements. and Views are used for SELECT statements.

A View will create itself in Memory, and depending on the type of Joins, Data and if there is any aggregation done, it could be a quite memory hungry View.
Stored procedures do all their processing either using Temp Hash Table e.g #tmpTable1 or in memory using @tmpTable1. Depending on what you want to tell it to do.
A Stored Procedure is like a Function, but is called Directly by its name. instead of Functions which are actually used inside a query itself.
Obviously most of the time Memory tables are faster, if you are not retrieveing alot of data.

Views can be used in the Stored Procedure but the Stored Procedure cannot be used in Views...!
Stored procedure will accept input parameters so that a single procedure can be used over the network by several clients using different input data. Stored procedure will reduce network traffic and increase the performance. If we modify stored procedure all the clients will get the updated stored procedure.

CREATE View vw_user_profile AS 
Select A.user_id, B.profile_description
FROM tbl_user A left join tbl_profile B on A.user_id = b.user_id

mysql> drop PROCEDURE auth_user_proc;
mysql> DELIMITER //
CREATE PROCEDURE auth_user_proc
(IN con CHAR(20))
BEGIN
SELECT last_login, username FROM auth_user
WHERE username = con;
END //
DELIMITER ;
mysql> CALL auth_user_proc('ldapuser1');
+---------------------+-----------+
| last_login          | username  |
+---------------------+-----------+
| 2017-07-20 13:36:26 | ldapuser1 |
+---------------------+-----------+
1 row in set (0.00 sec)

Query OK, 0 rows affected (0.00 sec)
PL/SQL Triggers:
A trigger is a special kind of stored procedure that automatically executes when an event occurs in the database server. DML triggers execute when a user tries to modify data through a data manipulation language DML event(DML events are INSERT, UPDATE, or DELETE statements on a table or view). 

DDL triggers execute in response to a variety of data definition language DDL events (e.g. Transact-SQL CREATE, ALTER, and DROP statements, and certain system stored procedures that perform DDL-like operations). 

Benefits of Triggers
•	Generating some derived column values automatically
•	Enforcing referential integrity
•	Event logging and storing information on table access
•	Auditing
•	Synchronous replication of tables
•	Imposing security authorizations
•	Preventing invalid transactions
Creating Triggers
The syntax for creating a trigger is −
CREATE [OR REPLACE ] TRIGGER trigger_name  
{BEFORE | AFTER | INSTEAD OF }  
{INSERT [OR] | UPDATE [OR] | DELETE}  
[OF col_name]  
ON table_name  
[REFERENCING OLD AS o NEW AS n]  
[FOR EACH ROW]  
WHEN (condition)   
DECLARE 
   Declaration-statements 
BEGIN  
   Executable-statements 
EXCEPTION 
   Exception-handling-statements 
END; 
Example
To start with, we will be using the CUSTOMERS table we had created and used in the previous chapters −
Select * from customers;  

+----+----------+-----+-----------+----------+ 
| ID | NAME     | AGE | ADDRESS   | SALARY   | 
+----+----------+-----+-----------+----------+ 
|  1 | Ramesh   |  32 | Ahmedabad |  2000.00 | 
|  2 | Khilan   |  25 | Delhi     |  1500.00 | 
|  3 | kaushik  |  23 | Kota      |  2000.00 | 
|  4 | Chaitali |  25 | Mumbai    |  6500.00 | 
|  5 | Hardik   |  27 | Bhopal    |  8500.00 | 
|  6 | Komal    |  22 | MP        |  4500.00 | 
+----+----------+-----+-----------+----------+ 
The following program creates a row-level trigger for the customers table that would fire for INSERT or UPDATE or DELETE operations performed on the CUSTOMERS table. This trigger will display the salary difference between the old values and new values −
CREATE OR REPLACE TRIGGER display_salary_changes 
BEFORE DELETE OR INSERT OR UPDATE ON customers 
FOR EACH ROW 
WHEN (NEW.ID > 0) 
DECLARE 
   sal_diff number; 
BEGIN 
   sal_diff := :NEW.salary  - :OLD.salary; 
   dbms_output.put_line('Old salary: ' || :OLD.salary); 
   dbms_output.put_line('New salary: ' || :NEW.salary); 
   dbms_output.put_line('Salary difference: ' || sal_diff); 
END; 
/ 
When the above code is executed at the SQL prompt, it produces the following result −
Trigger created.
The following points need to be considered here −
•	OLD and NEW references are not available for table-level triggers, rather you can use them for record-level triggers.
•	If you want to query the table in the same trigger, then you should use the AFTER keyword, because triggers can query the table or change it again only after the initial changes are applied and the table is back in a consistent state.
•	The above trigger has been written in such a way that it will fire before any DELETE or INSERT or UPDATE operation on the table, but you can write your trigger on a single or multiple operations, for example BEFORE DELETE, which will fire whenever a record will be deleted using the DELETE operation on the table.
Triggering a Trigger
Let us perform some DML operations on the CUSTOMERS table. Here is one INSERT statement, which will create a new record in the table −
INSERT INTO CUSTOMERS (ID,NAME,AGE,ADDRESS,SALARY) 
VALUES (7, 'Kriti', 22, 'HP', 7500.00 ); 
When a record is created in the CUSTOMERS table, the above create trigger, display_salary_changes will be fired and it will display the following result −
Old salary: 
New salary: 7500 
Salary difference:
Because this is a new record, old salary is not available and the above result comes as null. Let us now perform one more DML operation on the CUSTOMERS table. The UPDATE statement will update an existing record in the table −
UPDATE customers 
SET salary = salary + 500 
WHERE id = 2; 
When a record is updated in the CUSTOMERS table, the above create trigger, display_salary_changes will be fired and it will display the following result −
Old salary: 1500 
New salary: 2000 
Salary difference: 500 

http://www.joinfu.com/2005/12/managing-many-to-many-relationships-in-mysql-part-1/
https://dba.stackexchange.com/questions/10199/how-should-i-design-a-relationship-table-for-friendship
http://etutorials.org/Programming/PHP+MYSQL.+Programming+for+beginners/Chapter+9+Data+Normalization/Building+a+Link+Table+for+Many-Many+Relationships/
https://www.ntu.edu.sg/home/ehchua/programming/sql/Relational_Database_Design.html
https://stackoverflow.com/questions/19714308/mysql-how-to-insert-into-table-that-has-many-to-many-relationship


Knowing database name
mysql> use XE;
Database changed
mysql> select database();
+------------+
| database() |
+------------+
| XE         |
+------------+
1 row in set (0.00 sec)
Group By:
Group by is often used with aggregate functions( COUNT, MAX, MIN, SUM, AVG) to group the result set by one or more columns.
e.g.
SELECT COUNT (CustomerID), Country
FROM Customers
GROUP BY Country;
ACID Properties
A transaction is a very small unit of a program and it may contain several low-level tasks. A transaction in a database system must maintain Atomicity, Consistency, Isolation, and Durability − commonly known as ACID properties − in order to ensure accuracy, completeness, and data integrity.
•	Atomicity − This property states that a transaction must be treated as an atomic unit, that is, either all of its operations are executed or none. There must be no state in a database where a transaction is left partially completed. States should be defined either before the execution of the transaction or after the execution/abortion/failure of the transaction.
•	Consistency − The database must remain in a consistent state after any transaction. No transaction should have any adverse effect on the data residing in the database. If the database was in a consistent state before the execution of a transaction, it must remain consistent after the execution of the transaction as well.
•	Isolation − In a database system where more than one transaction are being executed simultaneously and in parallel, the property of isolation states that all the transactions will be carried out and executed as if it is the only transaction in the system. No transaction will affect the existence of any other transaction.
•	Durability − The database should be durable enough to hold all its latest updates even if the system fails or restarts. If a transaction updates a chunk of data in a database and commits, then the database will hold the modified data. If a transaction commits but the system fails before the data could be written on to the disk, then that data will be updated once the system springs back into action.
Note: 
Horizontal scaling means that you scale by adding more machines into your pool of resources whereas Vertical scaling means that you scale by adding more power (CPU, RAM) to an existing machine.
Following is a list of differences between SQL and NoSQL database:
Index 	SQL	NoSQL
1)	Databases are categorized as Relational Database Management System (RDBMS).	NoSQL databases are categorized as Non-relational or distributed database system.
2)	SQL databases have fixed or static or predefined schema.	NoSQL databases have dynamic schema.
3)	SQL databases display data in form of tables so it is known as table-based database.	NoSQL databases display data as collection of key-value pair, documents, graph databases or wide-column stores.
4)	SQL databases are vertically scalable.	NoSQL databases are horizontally scalable.
5)	SQL databases use a powerful language "Structured Query Language" to define and manipulate the data.	In NoSQL databases, collection of documents are used to query the data. It is also called unstructured query language. It varies from database to database.
6)	SQL databases are best suited for complex queries.	NoSQL databases are not so good for complex queries because these are not as powerful as SQL queries.
7)	SQL databases are not best suited for hierarchical data storage.	NoSQL databases are best suited for hierarchical data storage.
8)	MySQL, Oracle, Sqlite, PostgreSQL and MS-SQL etc. are the example of SQL database.	MongoDB, BigTable, Redis, RavenDB, Cassandra, Hbase, Neo4j, CouchDB etc. are the example of nosql database

SQL vs NoSQL CRUD Syntax
Creating, reading updating and deleting data is the basis of all database systems. In essence —
•	SQL is a lightweight declarative language. It’s deceptively powerful, and has become an international standard, although most systems implement subtly different syntaxes.
•	NoSQL databases use JavaScripty-looking queries with JSON-like arguments! Basic operations are simple, but nested JSON can become increasingly convoluted for more complex queries.
A quick comparison:
SQL	NoSQL
insert a new book record
INSERT INTO book (
  `ISBN`, `title`, `author`
)
VALUES (
  '9780992461256', 
  'Full Stack JavaScript', 
  'Colin Ihrig & Adam Bretz'
);	db.book.insert({
  ISBN: "9780992461256",
  title: "Full Stack JavaScript",
  author: "Colin Ihrig & Adam Bretz"
});
update a book record
UPDATE book
SET price = 19.99
WHERE ISBN = '9780992461256'	db.book.update(
  { ISBN: '9780992461256' },
  { $set: { price: 19.99 } }
);
return all book titles over $10
SELECT title FROM book
WHERE price > 10;	db.book.find(
  { price: { &gt;: 10 } },
  { _id: 0, title: 1 }
);
The second JSON object is known as a projection: it sets which fields are returned (_id is returned by default so it needs to be unset).
count the number of SitePoint books
SELECT COUNT(1) FROM book
WHERE publisher_id = 'SP001';	db.book.count({
  "publisher.name": "SitePoint"
});
This presumes denormalized documents are used.
return the number of book format types
SELECT format, COUNT(1) AS `total`
FROM book
GROUP BY format;	db.book.aggregate([
  { $group:
    { 
      _id: "$format", 
      total: { $sum: 1 } 
    }
  }
]);
This is known as aggregation: a new set of documents is computed from an original set.
delete all SitePoint books
DELETE FROM book
WHERE publisher_id = 'SP001';
Alternatively, it’s possible to delete the publisher record and have this cascade to associated book records if foreign keys are specified appropriately.	db.book.remove({
  "publisher.name": "SitePoint"
});
SQL vs NoSQL Performance
Perhaps the most controversial comparison, NoSQL is regularly quoted as being faster than SQL. This isn’t surprising; NoSQL’s simpler denormalized store allows you to retrieve all information about a specific item in a single request. There’s no need for related JOINs or complex SQL queries.
That said, your project design and data requirements will have most impact. A well-designed SQL database will almost certainly perform better than a badly designed NoSQL equivalent and vice versa.
Normalization in DBMS: 1NF, 2NF, 3NF and BCNF in Database
Normalization is a process of organizing the data in database to avoid data redundancy, insertion anomaly, update anomaly & deletion anomaly. Let’s discuss about anomalies first then we will discuss normal forms with examples.
Anomalies in DBMS
There are three types of anomalies that occur when the database is not normalized. These are – Insertion, update and deletion anomaly. Let’s take an example to understand this.
Example: Suppose a manufacturing company stores the employee details in a table named employee that has four attributes: emp_id for storing employee’s id, emp_name for storing employee’s name, emp_address for storing employee’s address and emp_dept for storing the department details in which the employee works. At some point of time the table looks like this:
emp_id	emp_name	emp_address	emp_dept
101	Rick	Delhi	D001
101	Rick	Delhi	D002
123	Maggie	Agra	D890
166	Glenn	Chennai	D900
166	Glenn	Chennai	D004
The above table is not normalized. We will see the problems that we face when a table is not normalized.
Update anomaly: In the above table we have two rows for employee Rick as he belongs to two departments of the company. If we want to update the address of Rick then we have to update the same in two rows or the data will become inconsistent. If somehow, the correct address gets updated in one department but not in other then as per the database, Rick would be having two different addresses, which is not correct and would lead to inconsistent data.
Insert anomaly: Suppose a new employee joins the company, who is under training and currently not assigned to any department then we would not be able to insert the data into the table if emp_dept field doesn’t allow nulls.
Delete anomaly: Suppose, if at a point of time the company closes the department D890 then deleting the rows that are having emp_dept as D890 would also delete the information of employee Maggie since she is assigned only to this department.
To overcome these anomalies we need to normalize the data. In the next section we will discuss about normalization.
Normalization
Here are the most commonly used normal forms:
•	First normal form(1NF)
•	Second normal form(2NF)
•	Third normal form(3NF)
•	Boyce & Codd normal form (BCNF)
First normal form (1NF)
As per the rule of first normal form, an attribute (column) of a table cannot hold multiple values. It should hold only atomic values.
Example: Suppose a company wants to store the names and contact details of its employees. It creates a table that looks like this:
emp_id	emp_name	emp_address	emp_mobile
101	Herschel	New Delhi	8912312390
102	Jon	Kanpur	8812121212
9900012222
103	Ron	Chennai	7778881212
104	Lester	Bangalore	9990000123
8123450987
Two employees (Jon & Lester) are having two mobile numbers so the company stored them in the same field as you can see in the table above.
This table is not in 1NF as the rule says “each attribute of a table must have atomic (single) values”, the emp_mobile values for employees Jon & Lester violates that rule.
To make the table complies with 1NF we should have the data like this:
emp_id	emp_name	emp_address	emp_mobile
101	Herschel	New Delhi	8912312390
102	Jon	Kanpur	8812121212
102	Jon	Kanpur	9900012222
103	Ron	Chennai	7778881212
104	Lester	Bangalore	9990000123
104	Lester	Bangalore	8123450987
Second normal form (2NF)
A table is said to be in 2NF if both the following conditions hold:
•	Table is in 1NF (First normal form)
•	No non-prime attribute is dependent on the proper subset of any candidate key of table.
An attribute that is not part of any candidate key is known as non-prime attribute.
Example: Suppose a school wants to store the data of teachers and the subjects they teach. They create a table that looks like this: Since a teacher can teach more than one subjects, the table can have multiple rows for a same teacher.
teacher_id	subject	teacher_age
111	Maths	38
111	Physics	38
222	Biology	38
333	Physics	40
333	Chemistry	40
Candidate Keys: {teacher_id, subject}
Non prime attribute: teacher_age
The table is in 1 NF because each attribute has atomic values. However, it is not in 2NF because non prime attribute teacher_age is dependent on teacher_id alone which is a proper subset of candidate key. This violates the rule for 2NF as the rule says “no non-prime attribute is dependent on the proper subset of any candidate key of the table”.
To make the table complies with 2NF we can break it in two tables like this:
teacher_details table:
teacher_id	teacher_age
111	38
222	38
333	40
teacher_subject table:
teacher_id	subject
111	Maths
111	Physics
222	Biology
333	Physics
333	Chemistry
Now the tables comply with Second normal form (2NF).
Third Normal form (3NF)
A table design is said to be in 3NF if both the following conditions hold:
•	Table must be in 2NF
•	Transitive functional dependency of non-prime attribute on any super key should be removed.
An attribute that is not part of any candidate key is known as non-prime attribute.
In other words 3NF can be explained like this: A table is in 3NF if it is in 2NF and for each functional dependency X-> Y at least one of the following conditions hold:
•	X is a super key of table
•	Y is a prime attribute of table
An attribute that is a part of one of the candidate keys is known as prime attribute.
Example: Suppose a company wants to store the complete address of each employee, they create a table named employee_details that looks like this:
emp_id	emp_name	emp_zip	emp_state	emp_city	emp_district
1001	John	282005	UP	Agra	Dayal Bagh
1002	Ajeet	222008	TN	Chennai	M-City
1006	Lora	282007	TN	Chennai	Urrapakkam
1101	Lilly	292008	UK	Pauri	Bhagwan
1201	Steve	222999	MP	Gwalior	Ratan
 
Super keys: {emp_id}, {emp_id, emp_name}, {emp_id, emp_name, emp_zip}…so on
Candidate Keys: {emp_id}
Non-prime attributes: all attributes except emp_id are non-prime as they are not part of any candidate keys.
Here, emp_state, emp_city & emp_district dependent on emp_zip. And, emp_zip is dependent on emp_id that makes non-prime attributes (emp_state, emp_city & emp_district) transitively dependent on super key (emp_id). This violates the rule of 3NF.
To make this table complies with 3NF we have to break the table into two tables to remove the transitive dependency:
employee table:
emp_id	emp_name	emp_zip
1001	John	282005
1002	Ajeet	222008
1006	Lora	282007
1101	Lilly	292008
1201	Steve	222999
employee_zip table:
emp_zip	emp_state	emp_city	emp_district
282005	UP	Agra	Dayal Bagh
222008	TN	Chennai	M-City
282007	TN	Chennai	Urrapakkam
292008	UK	Pauri	Bhagwan
222999	MP	Gwalior	Ratan
Boyce Codd normal form (BCNF)
It is an advance version of 3NF that’s why it is also referred as 3.5NF. BCNF is stricter than 3NF. A table complies with BCNF if it is in 3NF and for every functional dependency X->Y, X should be the super key of the table.
Example: Suppose there is a company wherein employees work in more than one department. They store the data like this:
emp_id	emp_nationality	emp_dept	dept_type	dept_no_of_emp
1001	Austrian	Production and planning	D001	200
1001	Austrian	Stores	D001	250
1002	American	design and technical support	D134	100
1002	American	Purchasing department	D134	600
Functional dependencies in the table above:
emp_id -> emp_nationality
emp_dept -> {dept_type, dept_no_of_emp}
Candidate key: {emp_id, emp_dept}
The table is not in BCNF as neither emp_id nor emp_dept alone are keys.
To make the table comply with BCNF we can break the table in three tables like this:
emp_nationality table:
emp_id	emp_nationality
1001	Austrian
1002	American
emp_dept table:
emp_dept	dept_type	dept_no_of_emp
Production and planning	D001	200
Stores	D001	250
design and technical support	D134	100
Purchasing department	D134	600
emp_dept_mapping table:
emp_id	emp_dept
1001	Production and planning
1001	stores
1002	design and technical support
1002	Purchasing department
Functional dependencies:
emp_id -> emp_nationality
emp_dept -> {dept_type, dept_no_of_emp}
Candidate keys:
For first table: emp_id
For second table: emp_dept
For third table: {emp_id, emp_dept}
This is now in BCNF as in both the functional dependencies left side part is a key.
Note that I’ve written ETL tool in python. It Extracts data from production Oracle database, Transforms it to SQL queries and Loads or imports it to MySQL database.
What does ETL tool do?
ETL is short for extract, transform, load, three database functions that are combined into one tool to pull data out of one database and place it into another database. Extract is the process of reading data from a database. The process of extracting data from source systems and bringing it into the data warehouse is commonly called ETL, which stands for extraction, transformation, and loading.
What is Informatica Tool?

Informatica is a data integration/ETL tool. It gathers data from various sources and loads it into distinctive targets. It just moves data from one place to another, but does not store anything.
What is ETL why is it used in Oracle?
Oracle Data Integrator (ODI) is an Extract, load and transform (ELT) (in contrast with the ETL common approach) tool produced by Oracle that offers a graphical environment to build, manage and maintain data integration processes in business intelligence systems.
What is meant by SQL Developer?
A free graphical user interface, Oracle SQL Developer allows database users and administrators to do their database tasks in fewer clicks and keystrokes. A productivity tool, SQL Developer's main objective is to help the end user save time and maximize the return on investment in the Oracle Database technology stack.

Providers of ETL Tools
Following is a list of the most popular commercial and freeware (open-source) ETL tools. 
Comercial ETL tools:
•	Ab Initio
•	Adeptia ETL
•	Alooma
•	Business Objects Data Services
•	Business Objects Data Integrator (BODI)
•	Confluent
•	DBSoftLab
•	Informatica PowerCenter
•	Information Server
•	Integration Services
•	iWay Software
•	Oracle Data Integrator (ODI)
•	Oracle Warehouse Builder (OWB)
•	Pervasive ETL
•	SAS ETL
•	Sybase ETL
Freeware, open source ETL tools:
•	Apach NiFi
•	Clover ETL
•	Jasper
•	Pentaho Data Integration (Kettle)
•	Talend Integrator Suite
 
Refer ETL_tool_MaSSEETL.pdf for sample UI interface of one of the ETL tool MaSSEETL.
Just for reference: https://docs.oracle.com/cd/E41507_01/epm91pbr3/eng/epm/penw/EPM_Routine_Details.pdf
ETL Routines
This table provides answers to questions about ETL routines.
Question	Answer
How are routines used?	Routines are used to make DataStage job code reusable. Routines are used in various parts of the job design. 95 routines are delivered as part of EPM ETL. These are present in a category called 'EPM_Routines'.
Where can one find the details for all the EPM Routines?	
me Category Short Description
GetSourceRowCount EPM_Routines\DeleteStrategy Gets the Source Row Count for the given Job.
RtnBuildSourceQuery EPM_Routines\DeleteStrategy Build Source Query Dynamically.
RtnConvertEPMStd EPM_Routines\DeleteStrategy Convert the Source Value to EPM Standards
RtnDeleteHashedRecords EPM_Routines\DeleteStrategy Delete a Record from the given Hashed File
RtnGetDelFlag EPM_Routines\DeleteStrategy This Routine is used to retrive the Delete Flag
RtnGetJobStartDTTM EPM_Routines\DeleteStrategy This Routine is used to retrive the Job Start Datetime
RtnStoreJobStartDTTM EPM_Routines\DeleteStrategy
Stores the Job Start DateTimeStamp to the Hashed File -
HASH_JOBSTARTDTTM
SBRtnWriteDelFlag EPM_Routines\DeleteStrategy Subroutine to Store the Delete Flag
AddToDate EPM_Routines\Generic
Adds the specified amount to the date. The field to which this amount
is to be added is specified by the format argument.
ClearHashFile EPM_Routines\Generic Clear the Hash File
ClearJobLog EPM_Routines\Generic Clear Job Log:- This utility will clear the log file of a job
DateDiff EPM_Routines\Generic Gives the number of days between two dates
DateDiffinMin EPM_Routines\Generic Gives the total time in minutes between the two dates
DateNotNullable EPM_Routines\Generic Returns Not Nullable Dates
DateToDateSIDDefault EPM_Routines\Generic Convert Date To Date Sid
ForceAbort EPM_Routines\Generic Logs a fatal error message in a job's log file and aborts the job.
ForceWarn EPM_Routines\Generic Logs a warning message in a job's log file.
GetAdmFunnel EPM_Routines\Generic
This routine is used to Generate an SQL Where clause for
J_Fact_PS_X_ADM_FUNNEL1 and J_Fact_PS_X_ADM_FUNNEL2
server jobs based on supplied Institution,Academic Career and Adit
Term .
GetBURole EPM_Routines\Generic Fetches the BU_ROLE corresponding to a specific table name
GetCharDefault EPM_Routines\Generic Gets Character type default value for Jobs.
GetCurrentDate EPM_Routines\Generic Get the current date
GetCurrentDateTime EPM_Routines\Generic Returns the current Date and Time.
GetDateDefault EPM_Routines\Generic Gets date type default value for Jobs.
GetDateSIDDefault EPM_Routines\Generic Returns Default DATE SID Value
GetEwSrcSysId EPM_Routines\Generic Gets the SRC_SYS_ID from HASH_PS_EW_OPTIONS hash file
GetFactMaxRecordSID EPM_Routines\Generic Gets the Last update time for the given Job.Used for Integer datatype.
GetFinalDateMonth EPM_Routines\Generic
Gets the last Date of the month (only the Date Part) of the given input
date column.
GetFinalDateofPreviousMonth EPM_Routines\Generic
Gets the Last Date of the Previous Month for a given Job (unless it
itself is the last date)
GetFirstDateMonth EPM_Routines\Generic Gets the First Date of the Month of the given input date column.
GetJobReport EPM_Routines\Generic This routine will generate the Job report for a given job
GetLastRecordID EPM_Routines\Generic
The routine is used to retrieve the MaxRecordID stored in a UniVerse
SbrtnDeleteVOC EPM_Routines\Generic Subroutine for deleting a VOC Entry
SbrtnSetVOC EPM_Routines\Generic Subroutine for Setting the VOC entry
TimeDiffSeconds EPM_Routines\Generic Compute the Time Difference in seconds between 2 timestamps.
ToChar EPM_Routines\Generic Converts a Date/Time data type to a string with the format specified.
ToCharn EPM_Routines\Generic Convert the number in string format to number
ToDate EPM_Routines\Generic Convert the format given to Date
ToInteger EPM_Routines\Generic Converts the input value to integer type.
ValidateHashLookup EPM_Routines\Generic
Validates the Hashed File whether it is having default values or valid
data
ValidateOWELkp EPM_Routines\Generic
This routine will identify whether the input values are valid values or the
default values
MCCBuildDateSQL EPM_Routines\MDW_Currency_Conversion
Build SQL to get required date value for a given date granularity record
name, DATE_DIM_REC.
MCCBuildSQL EPM_Routines\MDW_Currency_Conversion Build SQL for Currency Conversion
MCCE1RateCalc EPM_Routines\MDW_Currency_Conversion Currency Conversion Logic Using E1 Rate Table.
GenerateInputRH EPM_Routines\Trees_RecursiveHierarchy Code to write into a temp sequential file for RH
GetESourceNodeDescrLngSql EPM_Routines\Trees_RecursiveHierarchy Frame the SQL to get the Node Language Description(ESource Trees)
GetESourceNodeDescrSql EPM_Routines\Trees_RecursiveHierarchy Frame the SQL to get the Node Description(ESource Trees)
GetFlattenerEffdtClause EPM_Routines\Trees_RecursiveHierarchy Frame the Effdt subquery for the Flattener source DRS(EPM Trees).
GetNodeDescr EPM_Routines\Trees_RecursiveHierarchy Get the Node Descr for a given level
GetNodeDescrLng EPM_Routines\Trees_RecursiveHierarchy Get the Language Descr for a given level
GetNodeDescrLngSql EPM_Routines\Trees_RecursiveHierarchy Frame the SQL to get the Node language Description(EPM Trees).
GetNodeDescrSql EPM_Routines\Trees_RecursiveHierarchy Frame the SQL to get the Node Description(EPM Trees).
GetNodeID EPM_Routines\Trees_RecursiveHierarchy Get the Node ID for a given level.
GetParentInfo EPM_Routines\Trees_RecursiveHierarchy Get the Entity Information.
GetRecordNameDescr EPM_Routines\Trees_RecursiveHierarchy Get the Record Description
GetRhDnomParams EPM_Routines\Trees_RecursiveHierarchy Params for Recursive Hierarchy Denormalization
GetRhFlatParams EPM_Routines\Trees_RecursiveHierarchy Parameters for Recursive Hierarchy Flatenning
GetRHJobName EPM_Routines\Trees_RecursiveHierarchy Gets the Recursive Hierarchy Job Name to process.
GetStageRecname EPM_Routines\Trees_RecursiveHierarchy Gets the OWS record name for a given SRC record name
GetTreeDnomParams EPM_Routines\Trees_RecursiveHierarchy Parameters for Tree Denormalization
GetTreeFlatParams EPM_Routines\Trees_RecursiveHierarchy Parameters for Tree Flatenning
GetTreeFlatParamsLang EPM_Routines\Trees_RecursiveHierarchy Parameters for Tree Language jobs.
GetTreeJobName EPM_Routines\Trees_RecursiveHierarchy Gets the Tree Job Name to process.
GetTreeType EPM_Routines\Trees_RecursiveHierarchy Gets the Tree Type
InsertUVTable EPM_Routines\Trees_RecursiveHierarchy Insert Entity Information into UV Table.

