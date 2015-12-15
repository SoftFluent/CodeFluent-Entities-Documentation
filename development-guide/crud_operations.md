# CRUD operations

At design time, you modeled your business application using business entities, each and every one of them having properties, and relations to one another. Once the business model is ready, you can generate your **Business Object Model** (BOM).

By default, two classes are generated per entity: the first class corresponds to the entity class, and the other one to a collection of those entities. For instance a **Customer** entity will generate a **Customer** class and a **CustomerCollection** class which can manipulate several Customer instances. Apart from that notion, the BOM will be organized exactly as designed in the model.


## Basic CRUD

For instance, given the following model:

![](img/crud-01.png)

The **Customer** class will contain four properties: 
* **Id** of type **guid**
* **Email** of type **string**
* **FirstName** of type **string**
* **LastName** of type **string**
* **Orders** of type **OrderCollection**

Without any custom code, you then can:


### Load a customer

```csharp
// Loads the customer with the id 42
Customer customer = Customer.Load(42);
```

Which will call this stored procedure:



### Load all customers

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


### Save a customer

```csharp
Customer customer = new Customer();
customer.FirstName = "John";
customer.LastName = "Smith";
customer.Email = "john.smith@gmail.com";
customer.Save();
```

Which will call this stored procedure:

```sql
CREATE PROCEDURE [dbo].[Customer_Save]
(
 @Customer_Id [uniqueidentifier],
 @Customer_FirstName [nvarchar] (256) = NULL,
 @Customer_Email [nvarchar] (256) = NULL,
 @Customer_LastName [nvarchar] (256) = NULL,
 @_trackLastWriteUser [nvarchar] (64) = NULL,
 @_rowVersion [rowversion] = NULL
)
AS
SET NOCOUNT ON
DECLARE @error int, @rowcount int
DECLARE @tran bit; SELECT @tran = 0
IF @@TRANCOUNT = 0
BEGIN
 SELECT @tran = 1
 BEGIN TRANSACTION
END
IF(@_trackLastWriteUser IS NULL)
BEGIN
    SELECT DISTINCT @_trackLastWriteUser = SYSTEM_USER 
END
IF(@_rowVersion IS NOT NULL)
BEGIN
    UPDATE [Customer] SET
     [Customer].[Customer_FirstName] = @Customer_FirstName,
     [Customer].[Customer_Email] = @Customer_Email,
     [Customer].[Customer_LastName] = @Customer_LastName,
     [Customer].[_trackLastWriteUser] = @_trackLastWriteUser,
     [Customer].[_trackLastWriteTime] = GETDATE()
        WHERE (([Customer].[Customer_Id] = @Customer_Id) AND ([Customer].[_rowVersion] = @_rowVersion))
    SELECT @error = @@ERROR, @rowcount = @@ROWCOUNT
    IF(@error != 0)
    BEGIN
        IF @tran = 1 ROLLBACK TRANSACTION
        RETURN
    END
    IF(@rowcount = 0)
    BEGIN
        IF @tran = 1 ROLLBACK TRANSACTION
        RAISERROR (50001, 16, 1, 'Customer_Save')
        RETURN
    END
    SELECT DISTINCT [Customer].[_rowVersion] 
        FROM [Customer]
        WHERE ([Customer].[Customer_Id] = @Customer_Id)
END
ELSE
BEGIN
    INSERT INTO [Customer] (
        [Customer].[Customer_Id],
        [Customer].[Customer_FirstName],
        [Customer].[Customer_Email],
        [Customer].[Customer_LastName],
        [Customer].[_trackCreationUser],
        [Customer].[_trackLastWriteUser])
    VALUES (
        @Customer_Id,
        @Customer_FirstName,
        @Customer_Email,
        @Customer_LastName,
        @_trackLastWriteUser,
        @_trackLastWriteUser)
    SELECT @error = @@ERROR, @rowcount = @@ROWCOUNT
    IF(@error != 0)
    BEGIN
        IF @tran = 1 ROLLBACK TRANSACTION
        RETURN
    END
    SELECT DISTINCT [Customer].[_rowVersion] 
        FROM [Customer]
        WHERE ([Customer].[Customer_Id] = @Customer_Id)
END
IF @tran = 1 COMMIT TRANSACTION

RETURN
GO
```

### Delete a customer

```csharp
Customer customer = Customer.Load(42);
customer.Delete();
```

Which will call this stored procedure:

```sql
CREATE PROCEDURE [dbo].[Customer_Delete]
(
 @Customer_Id [uniqueidentifier],
 @_rowVersion [rowversion]
)
AS
SET NOCOUNT ON
DECLARE @error int, @rowcount int
DECLARE @tran bit; SELECT @tran = 0
IF @@TRANCOUNT = 0
BEGIN
 SELECT @tran = 1
 BEGIN TRANSACTION
END
UPDATE [Order] SET
 [Order].[Order_Customer_Id] = NULL
    WHERE ([Order].[Order_Customer_Id] = @Customer_Id)
SELECT @error = @@ERROR, @rowcount = @@ROWCOUNT
IF(@error != 0)
BEGIN
    IF @tran = 1 ROLLBACK TRANSACTION
    RETURN
END
DELETE FROM [Customer]
    WHERE (([Customer].[Customer_Id] = @Customer_Id) AND ([Customer].[_rowVersion] = @_rowVersion))
SELECT @error = @@ERROR, @rowcount = @@ROWCOUNT
IF(@rowcount = 0)
BEGIN
    IF @tran = 1 ROLLBACK TRANSACTION
    RAISERROR (50001, 16, 1, 'Customer_Delete')
    RETURN
END
IF @tran = 1 COMMIT TRANSACTION

RETURN
GO
```

## Relations

The BOM is a direct implementation of the model, consequently it applies the structural rules defined by the model. From the model we understand that:

* a customer can have several orders,
* an order can have only one customer,
* an order can contain several products.

The generated BOM conforms to those structural rules, see how to navigate in the BOM:

```csharp
Customer customer = customer.Load(42);
foreach (Order order in customer.Orders)
{
        Console.WriteLine("Order #" + order.Id + " contains:");
        foreach (Product product in order.Products)
        {
            Console.WriteLine(" + Product #" + product.Id + ": " + product.Label);
        }
}
```

As you can see relation properties are ```lazy loaded``` meaning that when getting the ```customer.Orders``` properties, if it's null, it's value will automatically be loaded from the database. Next times, as the property is non null, no extra round trip to the database will be done.

Creating relations is handled automatically:

```csharp
Customer customer = Customer.Load(42);
Product product = product.Load(1);
 
Order order = new Order();
order.Reference = Guid.NewGuid().ToString();
order.Status = OrderStatus.InProgress;
order.Customer = customer;
order.Products.Add(product);
order.Save();
```

The here-above code snippet creates an order the customer of id 42, which contains the product of id 1. Later in the code, if ever we iterate through the customer 42's orders, will see that it will contain the created order.