# C# Business Object Model generator

## Overview 

The **Business Object Model** producer can generate the business objects layer above the persistence layer. It generates .NET class for the entities declared in design model.

For each entity, the **BOM** producer generates two business objects : one object for the entity and one object for the entity set.

You can notice that the producer has generated two class for the **Customer** entity :

* the **Customer** class with the properties **Id, FirstName, LastName**
* the **CustomerCollection** class with the properties **Count** and **this[int index]**.

You can also notice that the two class have the same signature for the Insert, Save and Delete method. Fortunately, for the Load method, the signature is different : the Customer class has a Load method to load one entity and the CustomerCollection class has a LoadAll method to load all the entities.

## Configuration

Here are the configuration options.

### Compilation

| **Property** | **Description** |
| -- | -- |
| Compile With CodeFluent | Define if CodeFLuent must comppile the target assembly |
| Compile With Visual Studio | Determine if the target assembly will be compiled with Microsoft Visual Studio. |
| Generate Xml Documentation | Determines if Xml code documentation must be generated. |
| Output Name | Defines the compiled assembly name or path. |
| Target Lanquage | Define optiions specific to the chosen CodeDom provider. |


### Source production

| **Property** | **Description** |
| -- | -- |
| Data Annotations Modes | Determines how attributes in the ```System.ComponentModel.DataAnnotations``` namespace are added to the generated code. Only supported with a target framework higher or equal to 4. |
| Target Directory | Defines the directory path where scripts will be generated. |
| Target Project (read only) | Defines the Visual Studio target project to update when generating files. This is a file path to the project file, such as .csproj, etc. and is determined by the Target Directory property. |
| Target Project Layout | Defines options when updating the Visual Studio target project. |