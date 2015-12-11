# Paging

When displaying large amounts of data it's often best to only display a portion of the data, allowing the user to step through the data ten or so records at a time.

To allow you to read only a page of data, CodeFluent Entities automatically generates a paged version of all load, search and raw methods. For instance the default generated **LoadAll** method has an equivalent **PageLoadAll** method, and if you create a new **LoadByStatus** method in your model, you'll automatically get a **PageLoadByStatus** method.

As an example here's the signature of the PageLoadAll method:

```csharp
public static Sample.CustomerCollection PageLoadAll(int pageIndex, int pageSize, CodeFluent.Runtime.PageOptions pageOptions)
```