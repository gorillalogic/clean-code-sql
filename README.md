# Clean-code-sql
The final guide to write best SQL queries. 

## Table of Contents

- [Clean-code-sql](#clean-code-sql) 
  - [Introduction](#introduction)
  - [Indenting](#indenting)
  - [Select](#select)
  - [Joins](#joins)
  - [Tables](#tables)
  - [Columns](#columns)
  - [Procedures](#procedures)
  - [Fucntions](#fucntions)
  - [Views](#views)
  - [Union / Union All](#union--union-all)
  - [Comments](#comments)

## Introduction

These are guidelines about good practice to write good and readable SQL queries.

## Indenting

Indenting makes SQL query visually structured and easier to follow it.

Bad:
``` sql
SELECT d.DepartmentName,e.Name, e.LastName, e.Address, e.State, e.City, e.Zip FROM Departments AS d JOIN Employees AS e ON d.ID = e.DepartmentID WHERE d.DepartmentName != 'HR';
```

Good:
``` sql
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

<STRONG>SELECT *</STRONG>

When using <STRONG>SELECT *</STRONG>, all the columns will be returned in the same order as they are defined, so the data returned can change whenever the table definitions changes.

Bad:
``` sql
SELECT   *
FROM     Departments 
```

Good:
``` sql
SELECT    d.DepartmentName
        , d.Location
FROM     Departments AS d
```
**Table Aliases**

It is more readable to use aliases instead of writing columns with no table information.

Bad:
``` sql
SELECT   Name, DepartmentName
FROM     Departments 
JOIN     Employees  ON ID = DepartmentID;
```
Good:
``` sql
SELECT   e.Name, d.DepartmentName
FROM     Departments AS d
JOIN     Employees AS e ON d.ID = e.DepartmentID;
```

**SELECT DISTINCT**

SELECT DISTINCT is a practical way to remove duplicates from a query, however its use requires a high processing cost. Better select more fields to create unique results.

Bad:
``` sql
SELECT   DISTINCT Name, LastName, Address
FROM     Employees;
```
Good:
``` sql
SELECT   Name, LastName, Address, State, City, Zip
FROM     Employees;
```

[Back to top](#table-of-contents)
## Joins

Use the ANSI-Standard Join clauses instead of the old style joins.

Bad:
``` sql
SELECT   e.Name, d.DepartmentName
FROM     Departments AS d,
         Employees   AS e
WHERE    d.ID = e.DepartmentID;
```
Good:
``` sql
SELECT   e.Name, d.DepartmentName
FROM     Departments AS d
JOIN     Employees AS e ON d.ID = e.DepartmentID;
```

[Back to top](#table-of-contents)

## Tables

Some text

Bad:
```
example
```

Good:
```
example
```

## Columns

Some text

Bad:
```
example
```

Good:
```
example
```

## Procedures

Some text

Bad:
```
example
```

Good:
```
example
```

## Fucntions

Some text

Bad:
```
example
```

Good:
```
example
```

## Views

Don't use the word 'view' in the view's name 

Bad:
```sql
- ViewEmployeesDepartments
- EmployeeDeparments
```

Good:
```sql
- EmployeeDeparments
```

Don't  include Order by and Where conditions into the view

Bad:
```sql
SELECT  EmployeeId, Name, LastName, Address, State
FROM    Employees 
WHERE   EmployeeId > 0 ORDER BY Name
```

Good:
```sql
SELECT   Name, LastName, Address, State, City, Zip
FROM     Employees;
```

Use Alias with table + column to specify when the values come from

Bad:
```sql
SELECT   e.Name, d.Name
FROM     Departments AS d
JOIN     Employees AS e ON d.ID = e.DepartmentID;
```

Good:
```sql
SELECT   e.Name as EmployeeName, d.Name as DeparmentName
FROM     Departments AS d
JOIN     Employees AS e ON d.ID = e.DepartmentID;
```

## Union / Union All

Some text

Bad:
```
example
```

Good:
```
example
```

## Comments

Some text

Bad:
```
example
```

Good:
```
example
```


