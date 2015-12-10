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
| xxx | xxx |


### Source production

| **Property** | **Description** |
| -- | -- |
| xxx | xxx |
| xxx | xxx |
| xxx | xxx |
| xxx | xxx |