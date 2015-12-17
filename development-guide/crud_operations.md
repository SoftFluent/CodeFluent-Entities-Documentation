# CRUD operations







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