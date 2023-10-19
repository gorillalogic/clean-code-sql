<div dir="rtl">

# کد نویسی تمیز در زبان sql

یک راهنما برای اینکه کدهای تمیزی بنویسید.

## جدول محتوا

- [کد نویسی تمیز در sql](#clean-code-sql)
  - [جدول محتوا](#table-of-contents)
  - [مقدمه](#introduction)
  - [تورفتگی ها و فاصله ها](#indenting)
  - [دستور Select](#select)
  - [دستور Joins](#joins)
  - [جداول](#tables)
  - [ستون ها](#columns)
  - [Procedures](#procedures)
  - [Views](#views)
  - [نظرات](#comments)
  - [ماژولار کردن](#modularize)
  - [جداول موقت ](#temporary-tables)
  - [حلقه ی لوپ](#looping)
  - [عیب یابی](#when-troubleshooting)

## مقدمه

این دستورالعمل ها در مورد شیوه های خوب برای نوشتن کوئری های تمیزتر و بهتر هستند.

## تورفتگی ها و فاصله ها

استفاده از تورفتگی ها و فاصله های درست، باعث خوانایی بهتر کدها می شوند.

بد:

```sql
SELECT d.DepartmentName,e.Name, e.LastName, e.Address, e.State, e.City, e.Zip FROM Departments AS d JOIN Employees AS e ON d.ID = e.DepartmentID WHERE d.DepartmentName != 'HR';
```

خوب:

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

[برگشت به بالا](#table-of-contents)

## دستور Select

زمانی که از دستور select  استفاده میکنید، همه ی ستون ها به شکلی که order را تعریف کرده اید به شما نمایش داده می شوند( برگردانده می شوند)
بنابراین هر زمانی که شما order را تغییر دهید ، داده های برگشتی نیز تغییر می کنند.
بهتر است که شما فقط ستون هایی که نیاز دارید را انتخاب کنید.

بد:

```sql
SELECT   *
FROM     Departments
```

خوب:

```sql
SELECT    d.DepartmentName
        , d.Location
FROM     Departments AS d
```

**نام های مستعار برای جدول ها**

به جای نوشتن اسم ستون ها بدون هیچ توضیحاتی، ما از اسم مستعار جدول به همراه اسم ستون استفاده میکنیم.
با این کار دقیقا متوجه خواهیم شد که این ستون از کدام جدول بوده است.

بد:

```sql
SELECT   Name,
         DepartmentName
FROM     Departments
JOIN     Employees  ON ID = DepartmentID;
```

خوب:

```sql
SELECT   e.Name
       , d.DepartmentName
FROM     Departments AS d
JOIN     Employees AS e ON d.ID = e.DepartmentID;
```

**دستور SELECT DISTINCT**

SELECT DISTINCT روشی برای حذف موارد تکراری از نتایج کوئری است. 
اما توجه داشته باشید که استفاده از این دستور پردازش بیشتری انجام می دهد.
بنابراین بهتر است از ستون های بیشتری برای این کوئری استفاده کنید.

بد:

```sql
SELECT   DISTINCT Name,
         LastName,
         Address
FROM     Employees;
```

خوب:

```sql
SELECT   Name
       , LastName
       , Address
       , State
       , City
       , Zip
FROM     Employees;
```

**دستور EXISTS**

استفاده از دستور EXISTS  به جای دستور IN.
به دلیل اینکه دستور EXISTS برخلاف IN در هنگام یافتن نتیجه ی درست، خارج می شود.
ولی دستور IN کل جدول را اسکن می کند.

بد:

```sql
SELECT   e.name
       , e.salary
FROM Employee AS e
WHERE e.EmployeeID IN (SELECT   p.IdNumber
			FROM   Population AS p
			WHERE  p.country = "Canada"
			       AND   p.city = "Toronto");
```

خوب:

```sql
SELECT   e.name
       , e.salary
FROM Employee AS e
WHERE e.EmployeeID EXISTS (SELECT   p.IdNumber
			   FROM   Population AS p
			   WHERE  p.country = "Canada"
				  AND   p.city = "Toronto");
```

[برگشت به بالا](#table-of-contents)

## Joins

استفاده از JOIN به جای حالت قدیمی.

بد:

```sql
SELECT   e.Name,
         d.DepartmentName
FROM     Departments AS d,
         Employees   AS e
WHERE    d.ID = e.DepartmentID;
```

خوب:

```sql
SELECT   e.Name,
         d.DepartmentName
FROM     Departments AS d
JOIN     Employees AS e ON d.ID = e.DepartmentID;
```

[برگشت به بالا](#table-of-contents)

## جداول

برای اسم گذاری جدول ها از موارد زیر استفاده کنید:

- استفاده از اسم های مفرد
- استفاده از پاسکال کیس در اسم گذاری (در این روش حرف اول تمامی کلمه‌ها را با حروف بزرگ و مابقی حروف را بصورت کوچیک تایپ می‌کنند)
- استفاده از یک نمای یک طبقه بندی در قبل از اسم جدول([Person].[Address])


بد:

```sql
CREATE TABLE Addresses
```

خوب:

```sql
CREATE TABLE [Person].[Address]
```

[برگشت به بالا](#table-of-contents)

## Columns

برای اسم گذاری ستون ها به این موارد توجه داشته باشید:

- استفاده از نام های مفرد 
- استفاده از روش نوشتاری پاسکال کیس (در این روش حرف اول تمامی کلمه‌ها را با حروف بزرگ و مابقی حروف را بصورت کوچیک تایپ می‌کنند)
- از فرمت "TableName+ID" برای Primary key  ها استفاده کنید.
- بهتر است اسم ستون های شما دقیقا محتویات درون آن ستون را تعریف کند
- در اجرای این قوانین سرسخت باشید و خود را ملزم به استفاده از آن کنید.

بد:

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

خوب:

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

[برگشت به بالا](#table-of-contents)

## روند

برای نوشتن stored procedure های بهتر، این موارد را در نظر بگیرید: 

- از گزینه SET NOCOUNT ON در ابتدای رویه ذخیره شده استفاده کنید تا سرور از ارسال تعداد ردیف داده های تحت تأثیر برخی دستورات یا رویه های ذخیره شده به مشتری جلوگیری کند.
- سعی کنید DECLARATION و مقداردهی اولیه (SET) را در ابتدای رویه ذخیره شده بنویسید.
- تمام کلمات کلیدی SQL Server را با حرف CAPS بنویسید.
- از استفاده از 'sp\_' در ابتدای نام رویه ذخیره شده خودداری کنید.
- بخش های قبلی را در نظر بگیرید, [Indenting](#indenting) - [Select](#select) - [Joins](#joins).

بد:

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

خوب:

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

[برگشت به بالا](#table-of-contents)

## Views

هرگز از کلمه ی view در اسم ها استفاده نکنید .



بد:

```sql
- ViewEmployeesDepartments
- EmployeeDeparmentsView
```

خوب:

```sql
- EmployeeDeparments
```

هرگز از دستور order by و where با هم استفاده نکنید.
استفاده از هر دو مورد باعث پیچیدگی دستور خواهد شد.
بهتر است فیلترهای مرتب سازی را در نتایج بکار ببرید.

بد:

```sql
SELECT  EmployeeId,
        Name,
        LastName,
        Address,
        State
FROM    Employees
WHERE   EmployeeId > 0 ORDER BY Name
```

خوب:

```sql
SELECT   Name,
         LastName,
         Address,
         State,
         City,
         Zip
FROM     Employees;
```

استفاده از نام مستعار (جدول + اسم ستون) برای مشخص بودن نتایج برگشت داده شده.

بد:

```sql
SELECT   e.Name,
         d.Name
FROM     Departments AS d
JOIN     Employees AS e ON d.ID = e.DepartmentID;
```

خوب:

```sql
SELECT   e.Name as EmployeeName,
         d.Name as DeparmentName
FROM     Departments AS d
JOIN     Employees AS e ON d.ID = e.DepartmentID;
```

[برگشت به بالا](#table-of-contents)

## نظرات ها


نظرات مفید بنویسید. برای توضیح کدهایی که با خواندن خود کد متوجه می شویم نظر ننویسید.

بد:

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

خوب:

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

[برگشت به بالا](#table-of-contents)

## ماژولار کردن

هنگامی که یک کوئری بسیار طولانی دارید، فکر کردن درباره ی ماژولار کردن آن ایده ی خوبی است.
استفاده از (CTEs) می تواند به تجزیه و قابل فهم کردن آن کمک بسیاری بکند.

بد:

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

خوب:

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

[برگشت به بالا](#table-of-contents)

## جداول موقت

به جای استفاده از temporary table از متغیر table استفاده بکنید. زیرا که temporary table یک temp table در دیتابیس شما می سازد و بنابراین کوئری شما را آهسته تر می کند.


بد:

```sql
DECLARE TABLE #ListOWeekDays
(
  DyNumber INT,
  DayAbb   VARCHAR(40),
  WeekName VARCHAR(40)
)
```

خوب:

```sql
DECLARE @ListOWeekDays TABLE
(
  DyNumber INT,
  DayAbb   VARCHAR(40),
  WeekName VARCHAR(40)
)
```

[برگشت به بالا](#table-of-contents)

## Looping

از اجرای یک کوئری با استفاده از حلقه ی لوپ خودداری کنید.
کدنویسی با استفاده از حلقه ی لوپ سرعت اجرای کوئری را کند می کند.

بد:

```sql
WHILE @date <= @endMonth
BEGIN
    SET @date = DATEADD(d, 1, @date)
END
```

خوب:

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

[برگشت به بالا](#table-of-contents)

## عیب یابی

- در صورت در دسترس بودن ، از ابزار Execution Plan استفاده کنید.با این ابزار می توانید هشدارها و آمارها را مشاهده کنید.
- از کاما قبل از ستون ها استفاده کنید ، زمانی که مجبور به کامنت گذاری هستید می تواند مفید باشد.

[برگشت به بالا](#table-of-contents)
</div>