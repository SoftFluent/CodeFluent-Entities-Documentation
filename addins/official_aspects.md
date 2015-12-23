# Official Aspects

This section details and illustrates how to use CodeFluent Entities' shipped aspects.

## Overview

You can add a new Aspect to your CodeFluent Entities model from the **Aspects** node:

![](img/official-aspects-01.png)

For more information, see the [Aspect oriented modeling overview]().

## Localization Aspect

The Localization Aspect enables data localization (a.k.a. dynamic localization) support.

*Note: This aspect only supports C# as the BOM layer. As the persistence layer, **Microsoft SQL Server** and **Oracle Database** are supported.*

Pick the **SoftFluent.Localization.xml** file into the CodeFluent Entities installation directory.

![](img/official-aspects-02.png)

And specify some property as localizable:

![](img/official-aspects-03.png)

Now you can edit your instances with a new tab called **Localized Values**:

![](img/official-aspects-04.png)

And configure desired cultures:

![](img/official-aspects-05.png)

See [this blog post](http://blog.codefluententities.com/2014/09/15/localize-dynamic-resources-using-aspect/) to see how to use the BOM to save localized data.

## TextSearch Aspect

The **Text Search Aspect** adds methods to search an entity using tokenization techniques (also known as Middle-Of-Word text search technique). The search is case insensitive and accent insensitive. Say you want to create a screen in you application where users can search their contacts: the Text Search Aspect provides a way to implement it.

*Note: For now, the aspect is only supported using Microsoft SQL Server databases.*

*Note: This aspect does not support entities with composite or object keys.*

Pick the **SoftFluent.TextSearch.xml** file into the CodeFluent Entities installation directory.

![](img/official-aspects-06.png)

And specify some property to extract:

![](img/official-aspects-07.png)

Generating your application you'll notice that in the persistence layer:

* a new table named **TextSearch[KeyType]** was created,
* a new stored procedure named **[EntityName]_TextSearchTokens** was created.

Likewise, in the Business Object Model (BOM) the following classes were created:

* **Utilities\TextSearchEntityType.cs**
* **Utilities\TextSearchUtilities.cs**
* **Utilities\TextSearch[KeyType].cs** (one per key type used),
* **Utilities\TextSearch[KeyType]Collection.cs** (one per key type used).

If the Service Object Model Producer is configured the following contracts and classes will be added:

* **Services\ITextSearch[KeyType]Service.cs**
* **Services\TextSearch[KeyType]Service.cs**

In our example, the **Contact** entity is text searchable, ergo its resulting **ContactCollection** class will have two new methods:

* **TextSearchTokens(bool oneOrMore, string[] tokens)**
* **PageTextSearchTokens(int pageIndex, int pageSize, CodeFluent.Runtime.PageOptions pageOptions, bool oneOrMore, string[] tokens)**

The first one allows you to retrieve all results of the search, when the second one is a paged and sortable version of the first method.

### Upgrading Non-Searchable Data

If you set-up the **Text Search Aspect** from the very beginning of your application's life, tokens will be created and deleted along their represented instance. However, if you add the **Text Search Aspect** on a database already containing data, you'll have to build all tokens for instances that where created prior to the addition of the aspect. In that matter, the **TextSearchUtilities** class provides a method named **UpdateAll**.

Create yourself a console application in which the **UpdateAll** method is called once per searchable class. For instance:

```csharp
class Program
{
        static void Main(string[] args)
        {
             TextSearchUtilities.UpdateAll(typeof(ContactCollection));
        }
}
```

The **UpdateAll** method will create tokens for all existing lines.

## AssociationManage Aspect

This aspect adds method(s) to all entities having at least one **many to many** relation.

Added methods enable association management without loading the corresponding collections. Such methods can be very useful when creating your user interface.

Using this aspect is very straightforward: import the part in your project, specify it's run step, and it'll automatically on **many to many** entities.

![](img/official-aspects-08.png)

In the end, a method will be added to each entity holding a **many to many** relation to another entity. This method will be named **[RelationPropertyName]Manage**.

```csharp
public static bool ProductsManage(System.ComponentModel.CollectionChangeAction action, System.Guid orderReference, int productId)
{
    if ((action == default(System.ComponentModel.CollectionChangeAction)))
    {
        throw new System.ArgumentNullException("action");
    }
    if ((orderReference.Equals(CodeFluentPersistence.DefaultGuidValue) == true))
    {
        throw new System.ArgumentNullException("orderReference");
    }
    if ((productId == CodeFluentPersistence.DefaultInt32Value))
    {
        throw new System.ArgumentNullException("productId");
    }
    bool ret = CodeFluentPersistence.DefaultBooleanValue;
    CodeFluent.Runtime.CodeFluentPersistence persistence = CodeFluentContext.Get(OrderProcess.Constants.OrderProcessStoreName).Persistence;
    persistence.CreateStoredProcedureCommand(null, "Order", "ProductsManage");
    persistence.AddParameterEnumInt32("@action", action, new System.ComponentModel.CollectionChangeAction());
    persistence.AddParameter("@orderReference", orderReference);
    persistence.AddParameter("@productId", productId);
    System.Data.IDataReader reader = null;
    try
    {
        reader = persistence.ExecuteReader();
        if ((reader.Read() == true))
        {
            ret = ((bool)(ConvertUtilities.ChangeType(reader.GetValue(0), typeof(bool), null)));
        }
    }
    finally
    {
        if ((reader != null))
        {
            reader.Dispose();
        }
        persistence.CompleteCommand();
    }
    return ret;
}
```

*Known issue (Oracle only): This aspect adds a [EntityName]Manage stored procedure which calls two other delete and save stored procedure. Therefore, those stored procedures need to exist prior to the generation of the Manage procedure. As a consequence it is recommended to generate your model at least once before adding the aspect to avoid having an error such as:*

* *CodeFluent.Producers.Database.CodeFluentDatabaseProducerException: CF4703: Generated Oracle packages have errors: Error in 'Sample.CF_Order' line 125, position 26: 'PLS-00201: identifier 'CF_Product.SAVEORDERPRODUCTS' must be declared'.*
* *Error in 'Sample.CF_Order' line 125, position 26: 'PL/SQL: Statement ignored'.*
* *Error in 'Sample.CF_Order' line 126, position 26: 'PLS-00201: identifier 'CF_Product.DELETEORDERPRODUCTS' must be declared'.*
* *Error in 'Sample.CF_Order' line 126, position 26: 'PL/SQL: Statement ignored'.*

## AutoFormattable Aspect

The AutoFormattable aspect adds enhanced formatting capabilities to entities.

At production time, the AutoFormattable aspect iterates on entities with the autoFormattable attribute set to true, and adds an IFormattable implementation to the generated entity classes.

![](img/official-aspects-09.png)

To apply the AutoFormattable pattern at entity level, configure the entity as **Auto Formattable**:

![](img/official-aspects-10.png)

It also is possible to enable the pattern at the project level.

![](img/official-aspects-11.png)

**ToString** methods will be generated in the entity class as well as the collection class. Those methods will provide a string representation of the current class instance. The new methods are based on another class named **FormatUtilities** which is a helper class that also gets generated by the aspect.

The **FormatUtilities** helper class provides a set of methods useful in order to format string representations of an instance of an entity (or a collection of entities). For instance, a developer can specify a format value such as ```{0}:Name``` which indicates to return the **Name** property value of the specified instance.

```csharp
public string SalesmanName
{
    get
    {
        this.OnGetSalesmanName();
        return this._salesmanName;
    }
}
private void OnGetSalesmanName()
{
    if (this._salesmanName == null)
    {
        this._salesmanName = string.Format("{0:Name}", this.Salesman);
    }
}
```

*Note: The **FormatUtilities** generated by the **AutoFormattable** pattern, use lambda expressions. Hence, a project using the **AutoFormattable** pattern needs the **.NET 3.0 Framework** or upper to compile.*

## HierarchyDeepLoad Aspect

This aspect adds a **DeepLoadAll** method on a given set of entities. The **DeepLoadAll** method loads a complete inheritance hierarchy using a single server call.

*Note: This aspect only supports C# and SQL Server targets.*

![](img/official-aspects-13.png)

This example demonstrates how to use the **HierarchyDeepLoad** aspect to add a **DeepLoadAll** method to the **UserCollection** class, this **DeepLoadAll** method will load all **User** and all **Contractor** instances from the persistence layer.

![](img/official-aspects-12.png)

![](img/official-aspects-14.png)

The Microsoft SQL Server producer generates a **Customer_DeepLoadAllProc** stored procedure that loads all records from **User** and **Contractor** tables:

```sql
CREATE PROCEDURE [dbo].[User_DeepLoadAllProc]

AS
SET NOCOUNT ON
SELECT [User].[User_Id],[User].[User_Name],[User].[_typeName],[User].[_trackLastWriteTime],[User].[_trackCreationTime],[User].[_trackLastWriteUser],[User].[_trackCreationUser],[User].[_rowVersion]
FROM [User]
WHERE [User].[_typeName]='OrderProcess.Marketing.User'
SELECT [User].[User_Id],[User].[User_Name],[User].[_typeName],[User].[_trackLastWriteTime],[User].[_trackCreationTime],[User].[_trackLastWriteUser],[User].[_trackCreationUser],[User].[_rowVersion],[Contractor].[Contractor_Level]
FROM [User],[Contractor]
WHERE [User].[_typeName]='OrderProcess.Marketing.Contractor' AND [User].[User_Id]=[Contractor].[User_Id]

RETURN
GO
```

The Business Object Model producer adds a **DeepLoadAll** method to the **UserCollection** class, this method calls the **Customer_DeepLoadAllProc** stored procedure to load all **User** and **Contractor** instances.

```csharp
public static UserCollection DeepLoadAll()
{
    UserCollection items = new UserCollection();
    CodeFluentPersistence persistence = CodeFluentContext.Get("CodeFluent").Persistence;
    persistence.CreateStoredProcedureCommand("User_DeepLoadAllProc");
    using (IDataReader reader = persistence.ExecuteReader())
    {
        do
        {
            while (reader.Read())
            {
                string typeName = CodeFluentPersistence.GetReaderValue(reader, "_typeName", (string) null);
                User item = null;
                if (typeof(Contractor).FullName == typeName)
                {
                    item = new Contractor();
                }
                if (item == null)
                {
                    item = new User();
                }
                ((ICodeFluentEntity) item).ReadRecord(reader);
                items.BaseAdd(item);
                item.EntityState = CodeFluentEntityState.Unchanged;
            }
        }
        while (reader.NextResult());
        CodeFluentPersistence.CompleteCommand("CodeFluent");
    }
    return items;
}
```