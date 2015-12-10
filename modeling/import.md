# Import

The importer tool creates an in-memory representation of the source which will then be translated into a CodeFluent Entities model.

The creation of this in-memory representation is a two-step process where:

* An importer connects and iterates through database objects to provide a database independent representation of those concepts (Tables, Columns, Keys, etc.),
* Those persistence concepts are then translated into a corresponding CodeFluent Entities model (Entities, Properties, Relations, etc.).
 
Therefore each persistence system requires its own importer to translate its platform dependent concepts into those common concepts. Once this is done, the same import engine is shared by all importers and consequently they share a same set of features which is detailed in this topic. Those configuration parameters can be defined through the Importer Configuration page of the wizard:

The modeler provides a wizard to import your existing database or schema:

* Microsoft SQL Server
* Microsoft SQL Server CE
* Entity Framework

This wizard can be accessed from several ways depending your usage.

## Toolbar menu

This action will create and configure a brand new solution to import from an existing database or model.

![](img/import-01.png)

## Context menu

This action will import a database or model to the current CodeFluent Entities project.

![](img/import-02.png)

## Drag and drop

You can drag and drop tables from the SQL Server Object Explorer onto a CodeFluent Entities Surface. This will automatically trigger the wizard, configured for the tables you dragged and dropped.

![](img/import-03.png)

