# Custom methods

Custom methods are platform independent queries that will be translated:
* by the [Microsoft SQL Server code generator](../code-generators/microsoft_sql_server_code_generator.md) into T-SQL queries,
* by the [Business Object Model generator](code-generators/c_business_object_model_generator.md) into C# methods

## Load

Load methods allow you to define a method for loading sets of data.

This ```LoadByAvailable``` method on the **Product** entity:
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

Since the generated method manipulates sets of entities the method is generated in the **ProductCollection** class:

```csharp
[System.ComponentModel.DataObjectMethodAttribute(System.ComponentModel.DataObjectMethodType.Select, true)]
public static OrderProcess.Marketing.ProductCollection LoadByAvailable(bool availability)
{
    OrderProcess.Marketing.ProductCollection ret = OrderProcess.Marketing.ProductCollection.PageLoadByAvailable(int.MinValue, int.MaxValue, null, availability);
    return ret;
}


[System.ComponentModel.DataObjectMethodAttribute(System.ComponentModel.DataObjectMethodType.Select, true)]
public static OrderProcess.Marketing.ProductCollection PageLoadByAvailable(int pageIndex, int pageSize, CodeFluent.Runtime.PageOptions pageOptions, bool availability)
{
    if ((pageIndex < 0))
    {
        pageIndex = 0;
    }
    if ((pageSize < 0))
    {
        if ((pageOptions != null))
        {
            pageSize = pageOptions.DefaultPageSize;
        }
        else
        {
            pageSize = int.MaxValue;
        }
    }
    OrderProcess.Marketing.ProductCollection ret = new OrderProcess.Marketing.ProductCollection();
    System.Data.IDataReader reader = null;
    try
    {
        reader = OrderProcess.Marketing.ProductCollection.PageDataLoadByAvailable(pageOptions, availability);
        if ((reader == null))
        {
            return ret;
        }
        ret.LoadByAvailable(pageIndex, pageSize, pageOptions, reader, availability);
    }
    finally
    {
        if ((reader != null))
        {
            reader.Dispose();
        }
        CodeFluent.Runtime.CodeFluentPersistence.CompleteCommand(OrderProcess.Constants.OrderProcessStoreName);
    }
    return ret;
}

public static System.Data.IDataReader PageDataLoadByAvailable(CodeFluent.Runtime.PageOptions pageOptions, bool availability)
{
    CodeFluent.Runtime.CodeFluentPersistence persistence = CodeFluentContext.Get(OrderProcess.Constants.OrderProcessStoreName).Persistence;
    persistence.CreateStoredProcedureCommand(null, "Product", "LoadByAvailable");
    persistence.AddRawParameter("@availability", availability);
    if ((pageOptions != null))
    {
        System.Collections.IEnumerator enumerator = pageOptions.OrderByArguments.GetEnumerator();
        bool b;
        int index = 0;
        for (b = enumerator.MoveNext(); b; b = enumerator.MoveNext())
        {
            CodeFluent.Runtime.OrderByArgument argument = ((CodeFluent.Runtime.OrderByArgument)(enumerator.Current));
            persistence.AddParameter(string.Format("@_orderBy{0}", index), argument.Name);
            persistence.AddParameter(string.Format("@_orderByDirection{0}", index), ((int)(argument.Direction)));
            index = (index + 1);
        }
    }
    System.Data.IDataReader reader = CodeFluentContext.Get(OrderProcess.Constants.OrderProcessStoreName).Persistence.ExecuteReader();
    return reader;
}

private void LoadByAvailable(int pageIndex, int pageSize, CodeFluent.Runtime.PageOptions pageOptions, System.Data.IDataReader reader, bool availability)
{
    if ((reader == null))
    {
        throw new System.ArgumentNullException("reader");
    }
    if ((pageIndex < 0))
    {
        pageIndex = 0;
    }
    if ((pageSize < 0))
    {
        if ((pageOptions != null))
        {
            pageSize = pageOptions.DefaultPageSize;
        }
        else
        {
            pageSize = int.MaxValue;
        }
    }
    this.BaseList.Clear();
    this.BaseTable.Clear();
    int count = 0;
    int readCount = 0;
    bool readerRead;
    for (readerRead = reader.Read(); ((readerRead == true) 
                && ((count < this.MaxCount) 
                && (count < pageSize))); readerRead = reader.Read())
    {
        readCount = (readCount + 1);
        if ((CodeFluent.Runtime.CodeFluentPersistence.CanAddEntity(pageIndex, pageSize, pageOptions, readCount) == true))
        {
            OrderProcess.Marketing.Product product = new OrderProcess.Marketing.Product();
            ((CodeFluent.Runtime.ICodeFluentEntity)(product)).ReadRecord(reader);
            if ((this.BaseContains(product) == false))
            {
                this.BaseAdd(product);
                count = (count + 1);
            }
            product.EntityState = CodeFluent.Runtime.CodeFluentEntityState.Unchanged;
        }
    }
}
```

## LoadOne

LoadOne methods allow you to define a platform independent query, loading a single line of data.

This ```LoadByName``` on an **Employee** entity:

```sql
LOADONE(string Name)
WHERE Name=@Name
```

Will generate this stored procedure:


```sql
CREATE PROCEDURE [dbo].[Employee_LoadByName]
(@Name [nvarchar] (256))
AS
SELECT DISTINCT [Employee].[Employee_Id], [Employee].[Employee_Name], ... FROM [Employee]
    WHERE ([Employee].[Employee_Name] = @Name)
```

And this method on the Employee C# class:

```csharp
public static Employee LoadByName(string name)
```

## Search

See the [Search methods section](search_methods.md).

## Delete

Delete methods allow you to define custom query for deleting data.

This ```DeleteByName``` on an **Employee** entity:

```sql
DELETE(string Name)
WHERE Name STARTSWITH @Name
```

Looking at the generated stored procedures for the **Employee** entity, we can notice that there is a new stored procedure **Employee_DeleteByName**:

```sql
CREATE PROCEDURE [dbo].[Employee_DeleteByName] (@Name [nvarchar] (256))
AS
DELETE FROM [Employee] WHERE ([Employee].[Employee_Name] LIKE (@Name + '%'))
```

If we take a look at the generated code for the EmployeeCollection class, we can see that there is a new DeleteByName method.

```csharp
public static void DeleteByName(string name)
```

## Count

Count methods allow you to define a platform independent query, counting lines of data.

Since the generated method works on sets of entities, in the generated Business Object Model (BOM), the method is generated in the collection class.

**Count** methods are very similar to **Load** methods except that, instead of returning entities, they return an integer, as the result of the count operation.

This ```CountByCountry``` on an **Employee** entity:

```sql
COUNT(string countryName)
WHERE Address.Country LIKE @countryName
```

If we take a look at the generated stored procedures for the Employee entity, we can notice that there is a new stored procedure Employee_CountByCountry. 

```sql
CREATE PROCEDURE [dbo].[Employee_CountByCountry] (@countryName [nvarchar] (256))
AS
SELECT COUNT(*) FROM [Employee]
    LEFT OUTER JOIN [Address] ON ([Employee].[Employee_Address_Id] = [Address].[Address_Id])
    WHERE ([Address].[Address_Country] LIKE @countryName)
```

Looking at the generated code for the **EmployeeCollection** class, we can see that there is a new **CountByCountry** method which returns an **integer**.