# Custom methods

## Load

Load methods allow you to define a platform independent query, loading sets of data.

This query will be translated by the [Microsoft SQL Server code generator](../code-generators/microsoft_sql_server_code_generator.md) into a T-SQL query.

Since the generated method manipulates sets of entities, in the generated [Business Object Model](code-generators/c_business_object_model_generator.md), the method is generated in the collection class.

This method ```LoadByAvailable``` on the **Product** entity:
```sql
LOAD(bool availability)
WHERE IsAvailable=@availability
```
![](img/cfql-02.png)

Will be translated into this T-SQL stored procedure:

```sql
CREATE PROCEDURE [dbo].[Product_LoadByAvailable]
(
 @availability [bit],
 @_orderBy0 [nvarchar] (64) = NULL,
 @_orderByDirection0 [bit] = 0
)
AS
SET NOCOUNT ON
SELECT DISTINCT
    [Product].[Product_Id], 
    [Product].[Product_Price], 
    [Product].[Product_Name], 
    [Product].[Product_IsAvailable], 
    [Product].[_trackLastWriteTime], 
    [Product].[_trackCreationTime], 
    [Product].[_trackLastWriteUser], 
    [Product].[_trackCreationUser], 
    [Product].[_rowVersion] 
FROM [Product]
WHERE ([Product].[Product_IsAvailable] = @availability)

RETURN
GO
```

And will generate this C# source code:



## LoadOne

## Search

## Delete

## Count

## 