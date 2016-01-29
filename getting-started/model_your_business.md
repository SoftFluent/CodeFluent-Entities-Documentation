# Model your business


## Create your first project

Create a project from the Visual Studio templates in the CodeFluent Entities category.

![](img/model-your-business-00.png)

Select the "Blank CodeFluent Entities" Model template, and name it **OrderProcess.Model**, we will use the proposed "OrderProcess" namespace.

![](img/model-your-business-14.png)


## Create an entity

CodeFluent Entities allows you to define entities and enumerations. You can use the ribbon of top of the graphical modeler surface integrated into the Visual Studio.

![](img/model-your-business-01.png)

When you create an entity, you also need to choose a namespace (which will ultimately correspond to a .NET namespace).

![](img/model-your-business-02.png)

When you add a property to an entity, you also need to specify its data type.

![](img/model-your-business-03.png)

![](img/model-your-business-04.png)


## Create an enumeration

You may also need an enumeration to define a list of possible values of a property, for example a status.

![](img/model-your-business-05.png)

![](img/model-your-business-06.png)

And configure the possible values.

![](img/model-your-business-07.png)

![](img/model-your-business-08.png)

Then edit the data type of the "Status" property from the "Type Name" property grid value.

![](img/model-your-business-09.png)

![](img/model-your-business-10.png)


## Add relationships

You can also add property with an entity data type.

![](img/model-your-business-11.png)

The modeler will ask you to define the characteristics of the relationship you're going to create. Here we need to create a Customer property on the Order entity and assume that one Customer can have many Orders. Note you should always choose the phrase that obviously matches the names of the properties.

![](img/model-your-business-12.png)

You can also use a Entity Collection data type.

![](img/model-your-business-13.png)

You can also define a relation between two entities by holding SHIFT+click:

![](img/model-your-business-15.png)


