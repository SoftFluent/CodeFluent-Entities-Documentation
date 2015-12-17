# Save



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