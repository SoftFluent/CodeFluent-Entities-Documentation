# Microsoft SQL Server code generator

## Overview 

The **Microsoft SQL Server Producer** is a persistence producer which will translate your CodeFluent Entities model into a Microsoft SQL Server database.

Designing your model, you can define business concepts (e.g. entities, properties, methods, views), relations from a concept to another as well as the logic in which they all interact with one another. From this platform independent model, a meta-model is built which will be translated by producers to generate platform specific code.

From this meta-model, the Microsoft SQL Server Producer generates a set of **T-SQL scripts** which create data objects; translating platform independent concepts (e.g. a Customer entity) into platform specific code (e.g. a Customer table). On the same principles:

* Properties become columns,
* Methods become stored procedures,
* Instances become lines of data,
* Entity keys become primary keys,
* Entity relation properties become foreign keys,
* Views in your model become actual SQL views,
* etc.

All those data objects are created by the generated scripts. By default, those generated scripts are ran automatically when building your model.

Furthermore, the Microsoft SQL Server Producer has a **differential engine**, meaning that between generations the database isn't dropped and created over but **altered** (columns and procedures added, types changed). This feature is a key feature, since it allows to generate continuously, driving developments from the model. 

## Configuration

Here are the configuration options.

### Diff engine

| *Property* | *Description* |
| -- | -- |
| Connection String | Defines the connection string. If nothing is specified, the producer will use the connection string defined at project or store level. |
| Create Database | Determines if database must be created if is does not exists. |
| Create Diffs | Determines if diffs scripts must be created. |
| Drop Unused Columns | Determines if unused columns tables must be dropped. |
| Save Instances | Determines if instances will be saved. |
| Update Database | Determines if the CodeFluent Diff Engine will be used. |

### Script Generation

| *Property* | *Description* |
| -- | -- |
| Output Encoding | Defines the character encoding for scripts. If nothing is specified, the producer will use encoding defined at project level. |
| Produce Schemas | Determines if security schemas will be produced. |
| Produce View | Determines if persistent view will me produced. |
| Target Directory | xxx |
| Target Project | xxx |
| Target Project Layout | xxx |
| Target Version | xxx |

### Tools

| *Property* | *Description* |
| -- | -- |
| Management Studio | Launch Microsoft SQL Server Management Studio |