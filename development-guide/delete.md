# Delete

Use these instructions to delete a customer:

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