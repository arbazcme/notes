# Appendix A - SQL Essentials

This appendix contains practical SQL concepts that are frequently used in
projects and interviews but do not naturally fit inside the main SQL chapter.

---

# Navigation

- SQL Command Categories
- Common SQL Data Types
- Table Constraints
- CRUD On Tables
- NULL Handling
- UNION vs UNION ALL
- CASE Expression
- Frequently Used String Functions
- Frequently Used Date Functions
- Frequently Used Numeric Functions
- Frequently Used NULL Functions
- SQL Execution Order (Revision)
- Interview Cheat Sheet

---

# SQL Command Categories

| Category | Purpose | Examples |
|----------|----------|----------|
| DDL | Defines database structure | CREATE, ALTER, DROP, TRUNCATE |
| DML | Manipulates data | INSERT, UPDATE, DELETE |
| DQL | Retrieves data | SELECT |
| DCL | Access control | GRANT, REVOKE |
| TCL | Transaction control | COMMIT, ROLLBACK, SAVEPOINT |

---

# Common SQL Data Types

| Data Type | Used For |
|------------|----------|
| INT | Integers |
| BIGINT | Large Integers |
| DECIMAL(p,s) | Exact decimal values |
| FLOAT | Approximate decimals |
| CHAR(n) | Fixed-length strings |
| VARCHAR(n) | Variable-length strings |
| TEXT | Long text |
| DATE | Date only |
| TIME | Time only |
| DATETIME | Date and Time |
| BOOLEAN | True / False |

---

# Table Constraints

Constraints enforce rules on table data.

## PRIMARY KEY

- Unique
- Cannot be NULL
- One Primary Key per table

---

## FOREIGN KEY

Maintains Referential Integrity.

Child table values must exist in the parent table.

---

## UNIQUE

No duplicate values.

```sql
Email VARCHAR(100) UNIQUE;
```

---

## NOT NULL

Column must always contain a value.

```sql
Name VARCHAR(100) NOT NULL;
```

---

## DEFAULT

Assigns a value when none is provided.

```sql
Department VARCHAR(20) DEFAULT 'CSE';
```

---

## CHECK

Restricts valid values.

```sql
Age INT CHECK (Age >= 18);
```

---

## AUTO_INCREMENT (MySQL)

Automatically generates IDs.

```sql
RollNo INT AUTO_INCREMENT PRIMARY KEY;
```

SQL Server uses:

```sql
IDENTITY
```

---

## Complete Example

```sql
CREATE TABLE Student(
    RollNo INT AUTO_INCREMENT PRIMARY KEY,
    Name VARCHAR(100) NOT NULL,
    Email VARCHAR(100) UNIQUE,
    Age INT CHECK(Age>=18),
    Department VARCHAR(20) DEFAULT 'CSE'
);
```

---

# CRUD On Tables

## Create

```sql
CREATE TABLE Student(...);
```

---

## Read

```sql
SELECT *
FROM Student;
```

---

## Update

```sql
UPDATE Student
SET Department='ECE'
WHERE RollNo=101;
```

---

## Delete

```sql
DELETE
FROM Student
WHERE RollNo=101;
```

CRUD simply means:

```text
Create

↓

Read

↓

Update

↓

Delete
```

---

# NULL Handling

NULL means:

```text
Unknown

NOT

0

NOT

''
```

Wrong

```sql
WHERE Email = NULL;
```

Correct

```sql
WHERE Email IS NULL;
```

or

```sql
WHERE Email IS NOT NULL;
```

---

# UNION vs UNION ALL

UNION combines results while removing duplicates.

```sql
SELECT Name
FROM Student

UNION

SELECT Name
FROM Teacher;
```

UNION ALL keeps duplicates.

```sql
SELECT Name
FROM Student

UNION ALL

SELECT Name
FROM Teacher;
```

| UNION | UNION ALL |
|--------|-----------|
| Removes duplicates | Keeps duplicates |

---

# CASE Expression

Acts like an IF-ELSE.

```sql
SELECT Name,

CASE

WHEN Age>=18 THEN 'Adult'

ELSE 'Minor'

END AS Status

FROM Student;
```

Useful for creating custom output.

---

# Frequently Used String Functions

| Function | Example |
|----------|----------|
| LENGTH() | LENGTH(Name) |
| CHAR_LENGTH() | CHAR_LENGTH(Name) |
| LOWER() | LOWER(Name) |
| UPPER() | UPPER(Name) |
| CONCAT() | CONCAT(FirstName,' ',LastName) |
| SUBSTRING() | SUBSTRING(Name,1,3) |
| REPLACE() | REPLACE(Name,'A','X') |
| TRIM() | TRIM(Name) |
| LTRIM() | LTRIM(Name) |
| RTRIM() | RTRIM(Name) |
| LEFT() | LEFT(Name,2) |
| RIGHT() | RIGHT(Name,2) |

---

# Frequently Used Date Functions

| Function | Example |
|----------|----------|
| CURDATE() | Current Date |
| NOW() | Current Date & Time |
| YEAR() | YEAR(OrderDate) |
| MONTH() | MONTH(OrderDate) |
| DAY() | DAY(OrderDate) |
| DATEDIFF() | DATEDIFF(Date1,Date2) |
| DATE_ADD() | DATE_ADD(Date,INTERVAL 7 DAY) |
| DATE_SUB() | DATE_SUB(Date,INTERVAL 1 MONTH) |

---

# Frequently Used Numeric Functions

| Function | Example |
|----------|----------|
| ROUND() | ROUND(AVG(Salary),2) |
| CEIL() | CEIL(Price) |
| FLOOR() | FLOOR(Price) |
| ABS() | ABS(-20) |

---

# Frequently Used NULL Functions

## COALESCE()

Returns the first non-NULL value.

```sql
SELECT COALESCE(Phone,Email,'N/A');
```

---

## IFNULL() (MySQL)

```sql
SELECT IFNULL(Bonus,0);
```

Oracle equivalent:

```sql
NVL()
```

---

# SQL Execution Order (Revision)

Although SQL is written like this:

```sql
SELECT
FROM
WHERE
GROUP BY
HAVING
ORDER BY
LIMIT
```

The DBMS roughly executes it as:

```text
FROM

↓

WHERE

↓

GROUP BY

↓

HAVING

↓

SELECT

↓

DISTINCT

↓

ORDER BY

↓

LIMIT
```

Remember this order because it explains many SQL errors.

---

# Interview Cheat Sheet

| Question | Answer |
|-----------|--------|
| WHERE vs HAVING | WHERE filters rows. HAVING filters groups. |
| COUNT(*) vs COUNT(column) | COUNT(*) counts all rows. COUNT(column) ignores NULL values. |
| INNER vs LEFT JOIN | INNER returns matching rows. LEFT keeps every row from the left table. |
| DELETE vs TRUNCATE | DELETE removes rows. TRUNCATE removes all rows faster. |
| TRUNCATE vs DROP | TRUNCATE keeps the table. DROP deletes the table. |
| CHAR vs VARCHAR | CHAR is fixed length. VARCHAR is variable length. |
| PRIMARY KEY vs UNIQUE | PK is unique + NOT NULL. UNIQUE allows NULL (DBMS dependent). |
| PRIMARY KEY vs FOREIGN KEY | PK uniquely identifies a row. FK references another table. |
| View vs Table | View stores a query. Table stores data. |
| Procedure vs Function | Function returns a value. Procedure usually performs an action. |
| UNION vs UNION ALL | UNION removes duplicates. UNION ALL keeps duplicates. |
| NULL Comparison | Use IS NULL / IS NOT NULL, never = NULL. |

---

# Final Mental Model

```text
Database

↓

Tables

↓

Constraints

↓

CRUD

↓

SELECT

↓

Filtering

↓

Grouping

↓

Joining

↓

Subqueries

↓

Views

↓

Procedures

↓

Functions

↓

Triggers

↓

Practical SQL Essentials
```


# Keys & Constraints (Interview)

## Primary Key (PK)

A Primary Key uniquely identifies every row in a table.

Properties:

- Unique
- Cannot contain NULL
- One Primary Key per table
- Can be Composite (multiple columns)

Example:

```sql
CREATE TABLE Student(
    RollNo INT PRIMARY KEY,
    Name VARCHAR(100)
);
```

Valid

| RollNo | Name |
|--------|------|
|101|Ali|
|102|Sara|

Invalid

| RollNo | Name |
|--------|------|
|101|Ali|
|101|Sara|

Duplicate Primary Key.

---

## Composite Primary Key

A Primary Key made from multiple columns.

```sql
CREATE TABLE Enrollment(
    StudentID INT,
    CourseID INT,

    PRIMARY KEY(StudentID, CourseID)
);
```

Meaning

```
(StudentID, CourseID)
```

must be unique.

Valid

```
1  DBMS
1  OS
2  DBMS
```

Invalid

```
1  DBMS
1  DBMS
```

---

## Foreign Key (FK)

A Foreign Key references a Primary Key in another table.

```sql
CREATE TABLE Student(
    RollNo INT PRIMARY KEY,
    Name VARCHAR(100)
);
```

```sql
CREATE TABLE Enrollment(
    RollNo INT,
    Course VARCHAR(20),

    FOREIGN KEY(RollNo)
    REFERENCES Student(RollNo)
);
```

Meaning

```
Enrollment.RollNo

↓

must exist in

↓

Student.RollNo
```

---

## Referential Integrity

A Foreign Key prevents invalid references.

Student

| RollNo |
|--------|
|101|
|102|

Enrollment

| RollNo |
|--------|
|101|

✔ Valid

Enrollment

| RollNo |
|--------|
|999|

❌ Invalid

Student 999 doesn't exist.

---

## Can a Foreign Key contain NULL?

Yes.

```
Enrollment

RollNo

NULL
```

is allowed unless the column is declared

```sql
NOT NULL
```

---

## Can a Foreign Key contain duplicate values?

Yes.

Example

Enrollment

| RollNo |
|--------|
|101|
|101|
|101|

Perfectly valid.

Many enrollments can belong to one student.

---

## Can a table have multiple Foreign Keys?

Yes.

Example

```sql
CREATE TABLE Enrollment(

StudentID INT,

CourseID INT,

FOREIGN KEY(StudentID)
REFERENCES Student(StudentID),

FOREIGN KEY(CourseID)
REFERENCES Course(CourseID)

);
```

---

## Can a Foreign Key reference UNIQUE?

Yes.

It doesn't have to reference only a Primary Key.

It can reference any UNIQUE column.

---

## Self-Referencing Foreign Key

Useful for hierarchies.

```sql
CREATE TABLE Employee(

EmpID INT PRIMARY KEY,

Name VARCHAR(100),

ManagerID INT,

FOREIGN KEY(ManagerID)
REFERENCES Employee(EmpID)

);
```

Example

| EmpID | Name | ManagerID |
|------|------|-----------|
|1|Alice|NULL|
|2|Bob|1|
|3|Charlie|1|

Alice manages Bob and Charlie.

---

## ON DELETE CASCADE

Normally,

if a parent row is deleted,

the child rows become invalid.

Using

```sql
ON DELETE CASCADE
```

automatically deletes the child rows.

```sql
FOREIGN KEY(StudentID)

REFERENCES Student(StudentID)

ON DELETE CASCADE
```

Delete Student

↓

Enrollment rows are also deleted.

---

## ON DELETE SET NULL

Instead of deleting child rows,

their Foreign Key becomes NULL.

```sql
FOREIGN KEY(StudentID)

REFERENCES Student(StudentID)

ON DELETE SET NULL
```

Student deleted

↓

Enrollment.StudentID = NULL

---

# Interview Questions

### Why do we need Foreign Keys?

To maintain Referential Integrity.

---

### Can a Foreign Key be NULL?

Yes.

---

### Can a Foreign Key contain duplicates?

Yes.

---

### Can a table have multiple Foreign Keys?

Yes.

---

### Can a Foreign Key reference a UNIQUE column?

Yes.

---

### Difference between Primary Key and Foreign Key

| Primary Key | Foreign Key |
|-------------|-------------|
| Uniquely identifies rows | References another table |
| Unique | Duplicates allowed |
| NULL not allowed | NULL allowed (unless NOT NULL) |
| One per table | Multiple allowed |

---

### Difference between PRIMARY KEY and UNIQUE

| PRIMARY KEY | UNIQUE |
|-------------|---------|
| One per table | Multiple allowed |
| NULL not allowed | NULL allowed (DBMS dependent) |
| Identifies rows | Enforces uniqueness |


---

# Additional SQL Essentials

These are small but important SQL topics that are frequently used in projects and interviews but were not covered in the previous sections.

---

# USE Database

Before creating tables or running queries, select the database you want to work with.

```sql
USE CollegeDB;
```

Every query after this executes on the selected database until another database is chosen.

---

# INSERT

INSERT is used to add new rows into a table.

Insert values into every column:

```sql
INSERT INTO Student
VALUES
(101,'Arbaaz',21,'CSE');
```

Insert only selected columns:

```sql
INSERT INTO Student
(RollNo, Name)

VALUES

(102,'Rahul');
```

Insert multiple rows:

```sql
INSERT INTO Student
VALUES
(103,'Ali',20,'ECE'),
(104,'Sara',21,'CSE');
```

Notes:

- Columns omitted from the INSERT statement use their DEFAULT value (if defined).
- If no DEFAULT exists, SQL inserts NULL (if allowed).
- Otherwise the query fails.

---

# DROP DATABASE

Deletes an entire database.

```sql
DROP DATABASE CollegeDB;
```

Everything inside the database is permanently removed.

- Tables
- Views
- Stored Procedures
- Functions
- Triggers
- Indexes

Use carefully.

---

# Table Aliases

Aliases provide temporary names for tables.

Instead of writing

```sql
SELECT Student.Name
FROM Student;
```

we write

```sql
SELECT S.Name
FROM Student AS S;
```

`AS` is optional.

```sql
FROM Student AS S
```

is identical to

```sql
FROM Student S
```

Aliases make JOIN queries much easier to read.

---

# Column Aliases

Rename the output column without changing the actual table.

```sql
SELECT Name AS StudentName
FROM Student;
```

Output

```
StudentName
-----------
Arbaaz
Rahul
```

Only the displayed column name changes.

The table schema remains unchanged.

---

# LIMIT and OFFSET

LIMIT returns only a fixed number of rows.

```sql
SELECT *
FROM Student
LIMIT 5;
```

OFFSET skips rows before returning results.

```sql
SELECT *
FROM Student
LIMIT 5 OFFSET 10;
```

Meaning

```
Skip first 10 rows

↓

Return next 5 rows
```

This is commonly used for pagination.

---

# BETWEEN

Checks whether a value lies inside a range.

```sql
WHERE Age BETWEEN 18 AND 25;
```

Equivalent to

```sql
WHERE Age >= 18
AND Age <= 25;
```

Important:

**BETWEEN is inclusive.**

Both boundary values are included.

It is commonly used with

- Numbers
- Dates
- DateTime values

---

# LIKE Wildcards

LIKE performs pattern matching.

`%`

Matches zero or more characters.

Examples

```sql
'A%'
```

Starts with A.

```sql
'%A'
```

Ends with A.

```sql
'%A%'
```

Contains A.

---

`_`

Matches exactly one character.

Example

```sql
'A_'
```

Matches

```
AB
AC
A1
```

but does not match

```
ABC
```

---

# CREATE INDEX

Indexes speed up searching.

```sql
CREATE INDEX idx_name
ON Student(Name);
```

Think of an index like the index of a book.

Without an index,

the DBMS may scan every row.

With an index,

it can quickly locate matching rows.

Advantages

- Faster SELECT queries.

Disadvantages

- Slower INSERT.
- Slower UPDATE.
- Slower DELETE.
- Uses additional storage.

---

# DROP INDEX

Removes an index.

MySQL

```sql
DROP INDEX idx_name
ON Student;
```

Once removed,

future searches no longer benefit from that index.

---

# ON UPDATE CASCADE

Automatically updates child table Foreign Keys whenever the parent Primary Key changes.

```sql
FOREIGN KEY(StudentID)

REFERENCES Student(StudentID)

ON UPDATE CASCADE
```

Example

```
Student

101

↓

Update

↓

501
```

Enrollment automatically changes

```
101

↓

501
```

No manual UPDATE is required.

---

# ON DELETE RESTRICT

Prevents deleting a parent row while child rows still reference it.

Example

```
Student

↓

Enrollment
```

Attempt to delete Student

↓

Rejected

because Enrollment still depends on it.

This helps maintain Referential Integrity.

---

# Composite Foreign Key

A Foreign Key can reference multiple columns.

```sql
CREATE TABLE Enrollment(

StudentID INT,

CourseID INT,

FOREIGN KEY(StudentID, CourseID)

REFERENCES StudentCourse(StudentID, CourseID)

);
```

Used when the parent table has a Composite Primary Key.

---

# Useful MySQL Commands

Show every database

```sql
SHOW DATABASES;
```

Show every table in the current database

```sql
SHOW TABLES;
```

Display a table's schema

```sql
DESC Student;
```

or

```sql
DESCRIBE Student;
```

Shows

- Column Names
- Data Types
- NULL Allowed
- Keys
- Default Values
- Extra Information

Useful while debugging table structures.
