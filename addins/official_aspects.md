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

## HierarchyDeepLoad Aspect
## AssociationManage Aspect
## AutoFormattable Aspect