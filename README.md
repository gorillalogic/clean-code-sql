# Clean-code-sql

The final guide to write best SQL queries.

## Table of Contents

- [Clean-code-sql](#clean-code-sql)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Indenting](#indenting)
  - [Select](#select)
  - [Joins](#joins)
  - [Tables](#tables)
  - [Columns](#columns)
  - [Procedures](#procedures)
  - [Views](#views)
  - [Comments](#comments)
  - [Modularize](#modularize)
  - [Temporary tables](#temporary-tables)
  - [Looping](#looping)
  - [When troubleshooting](#when-troubleshooting)

## Introduction

These are guidelignes about good practices to write cleaner and better SQL queries.

## Indenting

Indenting makes SQL query visually structured and easier to follow it.

Bad:

```sql
SELECT d.DepartmentName,e.Name, e.LastName, e.Address, e.State, e.City, e.Zip FROM Departments AS d JOIN Employees AS e ON d.ID = e.DepartmentID WHERE d.DepartmentName != 'HR';
```

Good:

```sql
SELECT    d.DepartmentName
        , e.Name
        , e.LastName
        , e.Address
        , e.State
        , e.City
        , e.Zip
FROM     Departments AS d
JOIN     Employees AS e ON d.ID = e.DepartmentID
WHERE    d.Name != 'HR';
```

[Back to top](#table-of-contents)

## Select

<STRONG>SELECT \*</STRONG>

When using <STRONG>SELECT \*</STRONG>, all the columns will be returned in the same order as they are defined, so the data returned can change whenever the table definitions change.

Bad:

```sql
SELECT   *
FROM     Departments
```

Good:

```sql
SELECT    d.DepartmentName
        , d.Location
FROM     Departments AS d
```

**Table Aliases**

It is more readable to use aliases instead of writing columns with no table information.

Bad:

```sql
SELECT   Name,
         DepartmentName
FROM     Departments
JOIN     Employees  ON ID = DepartmentID;
```

Good:

```sql
SELECT   e.Name
       , d.DepartmentName
FROM     Departments AS d
JOIN     Employees AS e ON d.ID = e.DepartmentID;
```

**SELECT DISTINCT**

SELECT DISTINCT is a practical way to remove duplicates from a query, however its use requires a high processing cost. Better select more fields to create unique results.

Bad:

```sql
SELECT   DISTINCT Name,
         LastName,
         Address
FROM     Employees;
```

Good:

```sql
SELECT   Name
       , LastName
       , Address
       , State
       , City
       , Zip
FROM     Employees;
```

**EXISTS**

Prefer EXISTS to IN. Exists process exits as soon it finds the value, whereas IN scan the entire table.

Bad:

```sql
SELECT   e.name
       , e.salary
FROM Employee AS e
WHERE e.EmployeeID IN (SELECT   p.IdNumber
			FROM   Population AS p
			WHERE  p.country = "Canada"
			       AND   p.city = "Toronto");
```

Good:

```sql
SELECT   e.name
       , e.salary
FROM Employee AS e
WHERE e.EmployeeID EXISTS (SELECT   p.IdNumber
			   FROM   Population AS p
			   WHERE  p.country = "Canada"
				  AND   p.city = "Toronto");
```

[Back to top](#table-of-contents)

## Joins

Use the ANSI-Standard Join clauses instead of the old style joins.

Bad:

```sql
SELECT   e.Name,
         d.DepartmentName
FROM     Departments AS d,
         Employees   AS e
WHERE    d.ID = e.DepartmentID;
```

Good:

```sql
SELECT   e.Name,
         d.DepartmentName
FROM     Departments AS d
JOIN     Employees AS e ON d.ID = e.DepartmentID;
```

[Back to top](#table-of-contents)

## Tables

For naming tables take into consideration the following advices:

- Use singular names
- Use schema name prefix
- Use Pascal case

Bad:

```sql
CREATE TABLE Addresses
```

Good:

```sql
CREATE TABLE [Person].[Address]
```

[Back to top](#table-of-contents)

## Columns

For naming columns take into consideration the following advices:

- Use singular names
- Use Pascal case
- Name your primary keys using "[TableName]ID" format
- Be descriptive
- Be consistent

Bad:

```sql
CREATE TABLE [Person].[Address](
	[Address] [int] NOT NULL,
	[AddressLine1] [nvarchar](60) NOT NULL,
	[Address2] [nvarchar](60) NULL,
	[city] [nvarchar](30) NOT NULL,
	[State_ProvinceID] [int] NOT NULL,
	[postalCode] [nvarchar](15) NOT NULL,
	[ModifiedDate] [datetime] NOT NULL,
    CONSTRAINT [PK_Address_AddressID] PRIMARY KEY CLUSTERED ([Address])
);
```

Good:

```sql
CREATE TABLE [Person].[Address](
	[AddressID] [int] NOT NULL,
	[AddressLine1] [nvarchar](60) NOT NULL,
	[AddressLine2] [nvarchar](60) NULL,
	[City] [nvarchar](30) NOT NULL,
	[StateProvinceID] [int] NOT NULL,
	[PostalCode] [nvarchar](15) NOT NULL,
	[ModifiedDate] [datetime] NOT NULL,
    CONSTRAINT [PK_Address_AddressID] PRIMARY KEY CLUSTERED ([AddressID])
);
```

[Back to top](#table-of-contents)

## Procedures

To write incredible stored procedures, take into consideration the following advices:

- Use the SET NOCOUNT ON option in the beginning of the stored procedure to prevent the server from sending row counts of data affected by some statement or stored procedure to the client.
- Try to write the DECLARATION and initialization (SET) at the beginning of the stored procedure.
- Write all the SQL Server keywords in the CAPS letter.
- Avoid to use 'sp\_' at the begining of the stored procedure name.
- Take into consideration the previous sections, [Indenting](#indenting) - [Select](#select) - [Joins](#joins).

Bad:

```sql
CREATE OR ALTER PROCEDURE [HumanResources].[sp_UpdateEmployeePersonalInfo]
    @BusinessEntityID [int],
    @NationalIDNumber [nvarchar](15),
    @BirthDate [datetime],
    @MaritalStatus [nchar](1),
    @Gender [nchar](1)
AS
BEGIN
   --some code
END;
```

Good:

```sql
CREATE OR ALTER PROCEDURE [HumanResources].[UpdateEmployeePersonalInfo]
    @BusinessEntityID [int],
    @NationalIDNumber [nvarchar](15),
    @BirthDate [datetime],
    @MaritalStatus [nchar](1),
    @Gender [nchar](1)
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @messageLog nvarchar(100);

    SET @messageLog = 'Updating the employee personal info';

    --some code
END;
```

[Back to top](#table-of-contents)

## Views

Don't use the word 'view' in the view's name

Bad:

```sql
- ViewEmployeesDepartments
- EmployeeDeparmentsView
```

Good:

```sql
- EmployeeDeparments
```

Don't include Order by and Where conditions into the view

Bad:

```sql
SELECT  EmployeeId,
        Name,
        LastName,
        Address,
        State
FROM    Employees
WHERE   EmployeeId > 0 ORDER BY Name
```

Good:

```sql
SELECT   Name,
         LastName,
         Address,
         State,
         City,
         Zip
FROM     Employees;
```

Use Alias with table + column to specify when the values come from

Bad:

```sql
SELECT   e.Name,
         d.Name
FROM     Departments AS d
JOIN     Employees AS e ON d.ID = e.DepartmentID;
```

Good:

```sql
SELECT   e.Name as EmployeeName,
         d.Name as DeparmentName
FROM     Departments AS d
JOIN     Employees AS e ON d.ID = e.DepartmentID;
```

[Back to top](#table-of-contents)

## Comments

Write helpful comments and only when necessary. Do not write comments trying to explain the code that we will understand by reading the code itself.

Bad:

```sql
CREATE OR ALTER PROCEDURE [dbo].[uspLogError]
    @ErrorLogID [int] = 0 OUTPUT
AS
BEGIN
    SET NOCOUNT ON;
    -- Setting ErrorLogID to 0
    SET @ErrorLogID = 0;

	//some code
END;
```

Good:

```sql
CREATE OR ALTER PROCEDURE [dbo].[uspLogError]
    @ErrorLogID [int] = 0 OUTPUT
AS
BEGIN
    SET NOCOUNT ON;

    -- Output parameter value of 0 indicates that error
    -- information was not logged
    SET @ErrorLogID = 0;

	//some code
END;
```

[Back to top](#table-of-contents)

## Modularize

When writing a long query, thinking about modularizing is a good idea. Using common table expressions (CTEs) you can break down your code and make it easy to understand.

Bad:

```sql
SELECT   e.name
       , e.salary
FROM Employee AS e
WHERE e.EmployeeID EXISTS (SELECT   p.IdNumber
			   FROM   Population AS p
			   WHERE  p.country = "Canada"
				AND p.city = "Toronto")
      AND e.salary >= (SELECT	AVG(s.salary)
			FROM	salaries AS s
			WHERE	s.gender = "Female")
```

Good:

```sql
WITH toronto_ppl AS (
         SELECT   p.IdNumber
	 FROM   Population AS p
	 WHERE  p.country = "Canada"
		AND p.city = "Toronto"
)
, avg_female_salary AS (
        SELECT	AVG(s.salary) AS avgSalary
	 FROM	salaries AS s
	WHERE	s.gender = "Female"
)
SELECT   e.name
       , e.salary
FROM Employee AS e
WHERE e.EmployeeID EXISTS (SELECT IdNumber FROM toronto_ppl)
      AND e.salary >= (SELECT avgSalary FROM avg_female_salary)
```

[Back to top](#table-of-contents)

## Temporary tables

Prefer to use a Table variable when the result set is small instead of a Temporary table. A temp table will be created on the temp database and that will make your query slower

Bad:

```sql
DECLARE TABLE #ListOWeekDays
(
  DyNumber INT,
  DayAbb   VARCHAR(40),
  WeekName VARCHAR(40)
)
```

Good:

```sql
DECLARE @ListOWeekDays TABLE
(
  DyNumber INT,
  DayAbb   VARCHAR(40),
  WeekName VARCHAR(40)
)
```

[Back to top](#table-of-contents)

## Looping

Avoid running queries using a loop. Coding SQL queries in loops slows down the entire sequence

Bad:

```sql
WHILE @date <= @endMonth
BEGIN
    SET @date = DATEADD(d, 1, @date)
END
```

Good:

```sql
DECLARE @StartDate DateTime = '2021-06-01'
DECLARE @EndDate DateTime = '2021-06-29'

;WITH populateDates (dates) AS (

    SELECT @StartDate as dates
    UNION ALL
    SELECT DATEADD(d, 1, dates)
    FROM populateDates
    WHERE DATEADD(d, 1, dates)<=@EndDate

)
SELECT *
INTO dbo.SomeTable
FROM populateDates
```

[Back to top](#table-of-contents)

## When troubleshooting

- Use the Execution Plan tool if you have it available, with it you can see the statistics and warnings, which can help you find improvement areas.
- Use the comma before the column, it is useful when for some reason you have to comment out columns.

[Back to top](#table-of-contents)
