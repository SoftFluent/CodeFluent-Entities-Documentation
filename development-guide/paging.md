# Paging

When displaying large amounts of data it's often best to only display a portion of the data, allowing the user to step through the data ten or so records at a time.

To allow you to read only a page of data, CodeFluent Entities automatically generates a paged version of all load, search and raw methods. For instance the default generated **LoadAll** method has an equivalent **PageLoadAll** method, and if you create a new **LoadByStatus** method in your model, you'll automatically get a **PageLoadByStatus** method.

As an example here's the signature of the PageLoadAll method:

```csharp
public static Sample.CustomerCollection PageLoadAll(int pageIndex, int pageSize, CodeFluent.Runtime.PageOptions pageOptions)
```

* **pageIndex** corresponds to the index of the current page to load (0 for the first page, 1 for the second, etc.),
* **pageSize** corresponds to the size of a page: it's equivalent to the number of lines composing a page,
* **pageOptions** is an object principally used for dynamic sorting

The **LoadAll** method actually uses that method with a **pageIndex** at **int.MinValue**, a **pageSize** set at **int.MaxValue**, and **null** as **pageOptions**.

To retrieve the second page of 20 customers, you just need to use this method:

```csharp
CustomerCollection customers = CustomerCollection.PageLoadAll(2, 20, null);
```

You can use those paged methods across all .NET applications and for instance here's an example illustrating how to databind to it in ASP.NET Web Forms:

```xml
<asp:ObjectDataSource runat="server" ID="CustomerDataSource" SelectMethod="PageLoadAll"
                  TypeName="Sample.CustomerCollection" DataObjectTypeName="Sample.Customer"
                  EnablePaging="true" StartRowIndexParameterName="pageIndex" MaximumRowsParameterName="pageSize">
<SelectParameters>
        <asp:Parameter Name="pageIndex" Type="Int32" DefaultValue="0" />
        <asp:Parameter Name="pageSize" Type="Int32" DefaultValue="1000" />
        <cfw:PageOptionsParameter Name="pageOptions" DefaultPageSize="1000" />
    </SelectParameters>
</asp:ObjectDataSource>

<asp:GridView ID="GridView" runat="server" EmptyDataText="No data available"
    DataSourceID="CustomerDataSource" AutoGenerateColumns="True" AllowPaging="true" PageSize="100"
    CellPadding="5" CellSpacing="0" />
```