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

## Pagination

CodeFluent Entities generate **for every Load methods**:

* An object oriented method (example: **LoadAll**),
* A paginated one (example: **PageLoadAll**)
* An optimized one returning a **IDataReader** instead of objects (example: **DataLoadAll**)
* An optimized and paginated one (example: **PageDataLoadAll**)