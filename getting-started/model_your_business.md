# Model your business


## Create your first project

Create the project from the Visual Studio templates in the CodeFluent Entities category.

![](img/model-your-business-00.png)

Select the blank CodeFluent Entities Model template, we will name **OrderProcess.Model**, we will use the OrderProcess namespace as default.

![](img/model-your-business-14.png)


## Create an entity

CodeFluent Entities allows you to define entities and enumerations. You can use the ribbon of the graphical modeler, directly integrated into the Visual Studio.

![](img/model-your-business-01.png)

When you create an entity, you need to choose the namespace location.

![](img/model-your-business-02.png)

When you add a property to an entity, you need to specify the data type.

![](img/model-your-business-03.png)

![](img/model-your-business-04.png)


## Create an enumeration

You may also need an enumeration to constraint possible values of a property, for example a status.

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

This will ask you to define the relationship you are going to create. Here we need to create a Customer property on the Order entity and assume that one Customer can have many Orders.

![](img/model-your-business-12.png)

You can also use a Entity Collection data type.

![](img/model-your-business-13.png)

You can also define a relation between two entities by holding SHIFT+click:

![](img/model-your-business-15.png)

