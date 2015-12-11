# Search methods

Search methods are CodeFluent Query Language (**CFQL**) methods which allow you to define platform independent queries to implement searching capabilities in your application. Persistence producers will then translate those platform independent queries into actual SQL queries.

## Search

![](img/cfql-03.png)

For instance, searching for cinemas by name and a cinema identifier.

```SimpleSearch``` :

```sql
SEARCH(Name, CinemaId)
```

The Microsoft SQL Server code generator will produce the following stored procedure:

```sql
CREATE PROCEDURE [dbo].[Cinema_SimpleSearch]
(
@Name [nvarchar] (256) = NULL,
@CinemaId [nvarchar] (256) = NULL,
@_orderBy0 [nvarchar] (64) = NULL,
@_orderByDirection0 [bit] = 0
)
AS
SET NOCOUNT ON
DECLARE @sql nvarchar(max), @paramlist nvarchar(max)
SELECT @sql=
'SELECT DISTINCT [Cinema].[Cinema_Id], [Cinema].[Cinema_Name], [Cinema].[Cinema_CinemaId]
    FROM [Cinema]
    WHERE (1 = 1)'
SELECT @paramlist = '@Name nvarchar (256),
    @CinemaId nvarchar (256),
    @_orderBy0 nvarchar (64),
    @_orderByDirection0 bit'
IF @Name IS NOT NULL
    SELECT @sql = @sql + ' AND ([Cinema].[Cinema_Name] = @Name)'
IF @CinemaId IS NOT NULL
    SELECT @sql = @sql + ' AND ([Cinema].[Cinema_CinemaId] = @CinemaId)'
EXEC sp_executesql @sql, @paramlist,
    @Name,
    @CinemaId,
    @_orderBy0,
    @_orderByDirection0
RETURN
GO
```

*Note: The ```(1 = 1)``` statement is for development conveniences: it avoids us to create a special case for the first ```AND``` condition. Instead for each condition we add a ```WHERE (1 = 1)```, so we are sure the generated ```WHERE``` clause is present. Moreover, the SQL Server's query optimizer will remove such statements when compiling stored procedures. In the end, those ```(1 = 1)``` have no consequences.*

## Wildcards

### The Star (*)

The ```*``` wildcard is available in search methods. It indicates the method to search by all persistent properties of the entity.

```Search``` :
```sql
SEARCH(*)
```