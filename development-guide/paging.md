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