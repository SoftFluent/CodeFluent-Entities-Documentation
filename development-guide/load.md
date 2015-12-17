# Load

## Load

Use this instruction to load a customer by its id:

```csharp
// Loads the customer with the id 42
Customer customer = Customer.Load(42);
```

Which will call this stored procedure:


```sql
CREATE PROCEDURE [dbo].[Customer_Load]
(
 @Id [uniqueidentifier]
)
AS
SET NOCOUNT ON
SELECT DISTINCT [Customer].[Customer_Id], [Customer].[Customer_FirstName], [Customer].[Customer_Email], [Customer].[Customer_LastName], [Customer].[_trackLastWriteTime], [Customer].[_trackCreationTime], [Customer].[_trackLastWriteUser], [Customer].[_trackCreationUser], [Customer].[_rowVersion] 
    FROM [Customer]
    WHERE ([Customer].[Customer_Id] = @Id)

RETURN
GO
```

### LoadAll

Use this instruction to load all customers:

```csharp
CustomerCollection customers = Customer.LoadAll();
```

Which will call this stored procedure:


```sql
CREATE PROCEDURE [dbo].[Customer_LoadAll]
(
 @_orderBy0 [nvarchar] (64) = NULL,
 @_orderByDirection0 [bit] = 0
)
AS
SET NOCOUNT ON
SELECT DISTINCT [Customer].[Customer_Id], [Customer].[Customer_FirstName], [Customer].[Customer_Email], [Customer].[Customer_LastName], [Customer].[_trackLastWriteTime], [Customer].[_trackCreationTime], [Customer].[_trackLastWriteUser], [Customer].[_trackCreationUser], [Customer].[_rowVersion] 
    FROM [Customer]

RETURN
GO
```

## Performances

CodeFluent Entities generate **for every Load methods**:

* An object oriented method (example: **LoadAll**),
* A paginated one (example: **PageLoadAll**)
* An optimized one, returning a **IDataReader** instead of objects (example: **DataLoadAll**)
* An optimized and paginated one (example: **PageDataLoadAll**)

See below the **LoadAll** method for the Product entity collection class:

```csharp
[System.ComponentModel.DataObjectMethodAttribute(System.ComponentModel.DataObjectMethodType.Select, true)]
public static OrderProcess.Marketing.ProductCollection LoadAll()
{
    OrderProcess.Marketing.ProductCollection ret = OrderProcess.Marketing.ProductCollection.PageLoadAll(int.MinValue, int.MaxValue, null);
    return ret;
}
```

See below the **PageLoadAll** method:

```csharp
[System.ComponentModel.DataObjectMethodAttribute(System.ComponentModel.DataObjectMethodType.Select, true)]
public static OrderProcess.Marketing.ProductCollection PageLoadAll(int pageIndex, int pageSize, CodeFluent.Runtime.PageOptions pageOptions)
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
        reader = OrderProcess.Marketing.ProductCollection.PageDataLoadAll(pageOptions);
        if ((reader == null))
        {
            return ret;
        }
        ret.LoadAll(pageIndex, pageSize, pageOptions, reader);
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
```

See below the **PageDataLoadAll** method:

```csharp
public static System.Data.IDataReader PageDataLoadAll(CodeFluent.Runtime.PageOptions pageOptions)
{
    CodeFluent.Runtime.CodeFluentPersistence persistence = CodeFluentContext.Get(OrderProcess.Constants.OrderProcessStoreName).Persistence;
    persistence.CreateStoredProcedureCommand(null, "Product", "LoadAll");
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
```

All these methods use the same **Product_LoadAll** stored procedure:
```sql
CREATE PROCEDURE [dbo].[Product_LoadAll]
(
 @_orderBy0 [nvarchar] (64) = NULL,
 @_orderByDirection0 [bit] = 0
)
AS
SET NOCOUNT ON
SELECT DISTINCT [Product].[Product_Id], [Product].[Product_Price], [Product].[Product_Name], [Product].[Product_IsAvailable], [Product].[_trackLastWriteTime], [Product].[_trackCreationTime], [Product].[_trackLastWriteUser], [Product].[_trackCreationUser], [Product].[_rowVersion] 
    FROM [Product]

RETURN
GO
```