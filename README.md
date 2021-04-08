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
SELECT d.Name, COUNT(*) AS Employees FROM Departments AS d JOIN Employees AS e ON d.ID = e.DepartmentID WHERE d.Name != 'HR' HAVING COUNT(*) > 10 ORDER BY COUNT(*) DESC;
```

Good:
``` sql
SELECT   d.Name,
         COUNT(*) AS Employees
FROM     Departments AS d
JOIN     Employees AS e ON d.ID = e.DepartmentID
WHERE    d.Name != 'HR'
HAVING   COUNT(*) > 10
ORDER BY COUNT(*) DESC;
```
[Back to top](#table-of-contents)
## Select

Some text

Bad:
```
example
```

Good:
```
example
```

## Joins

Some text

Bad:
```
example
```

Good:
```
example
```

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

Some text

Bad:
```
example
```

Good:
```
example
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


