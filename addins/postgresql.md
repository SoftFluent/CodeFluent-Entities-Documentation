# PostgreSQL

The **PostgreSQL Producer** is a persistence code generator: it translates a CodeFluent Entities model into a database running on **PostgreSQL Server 8.4 and upper**. The database is created by generating SQL scripts which are then automatically ran on the configured server.

## Prerequisites

### .NET

The PostgreSQL Producer relies on the [Npgsql2](http://npgsql.projects.postgresql.org/) library, version **2.0.11**. CodeFluent Entities requires the Npgsql assembly.

### OSSP

The producer also requires the [libossp-uuid](http://www.ossp.org/pkg/lib/uuid/) package to be installed on the server.

### Windows

Please note that libossp-uuid is shipped with the Windows PostgreSQL 8.4+ standard installation package.

### Linux

You need to install the postgresql-contrib package.

## Generate PostgreSQL persistence layer

The PostgreSQL Producer is implemented by the **CodeFluent.Producers.PostgreSQLProducer** class, contained in the **CodeFluent.Producers.PostgreSQL** assembly.


The PostgreSQL Producer is available in the **Persistence Layer Producers** section of the **Add New Producer** window.

![](img/postgresql-01.png)

Which will generate scripts like this:

```
CREATE OR REPLACE FUNCTION "public"."Customer_Delete"
(
    "#Customer_Id" uuid,
    "#_rowVersion" bytea
)
RETURNS void
VOLATILE
CALLED ON NULL INPUT
AS $$
DECLARE
    "cf_refcursor" refcursor;
    
BEGIN
    DELETE FROM "public"."Customer"
        WHERE (("Customer"."Customer_Id" = "#Customer_Id") AND ("Customer"."_rowVersion" = "#_rowVersion"));
    IF NOT FOUND THEN
        RAISE EXCEPTION 'CodeFluent Runtime: Concurrency error';
    END IF;

END;
$$ LANGUAGE plpgsql;
//
//
```

## Application configuration

You need to configure the generated classes to use PostgreSQL as persistence layer (**persistenceTypeName**) and set a connection string indicating them where to connect to (**connectionString**). Here's a sample configuration for a sample application named **Northwind**:

```xml
<configSections>
  <section name="Northwind" type="CodeFluent.Runtime.CodeFluentConfigurationSectionHandler, CodeFluent.Runtime" />
</configSections>
<Northwind persistenceTypeName="PostgreSQL"
           connectionString="Server=localhost;Database=PostgreSQL_Test;User Id=postgres;Password=yourpassword;"
           postgresql-defaultSchema="public"
           postgresql-useParameterCache="true" />
```

*Notes: **postrgresql-defaultSchema** and **postgresql-useParameterCache** are optional and their respective default values are "public" and "true".*

The **default schema** defines the schema to be used when an entity doesn't have a schema. By default, the "public" schema is used.

The **postgresql-useParameterCache** attribute allows you to disable the use of the parameter cache system which stores lists of parameters for each stored procedure that is used.

## Supported features

The following features are supported:

* Generating schemas and cross-schemas relations
* Generating tables
* Generating constraints (primary keys and foreign keys)
* Generating sequences for auto-incremented columns
* Generating views
* Generating stored procedures (using SQL or PL/pgSQL)
* Generating instances
* Saving instances
* Saving Binary Large Objects (BLOB) instances in bytea columns

## Unsupported features

CFQL [Search](../development-guide/search_methods.md) methods


## Special cases

### Binary Large Objects (BLOB) management

Any binary large object defined in the model is stored in PostgreSQL in fields of type [bytea](http://www.postgresql.org/docs/8.4/static/datatype-binary.html), which implies **a max size of 1GB** per object.

### Creating raw methods

You can only write your raw methods in PL/pgSQL. Other languages are not supported.

### PL/pgSQL Functions

By default, if your method is returning something, the generated stored procedure skeleton will return a ```refcursor```. The return type can be changed through the **customReturnType** attribute on the method. By default a ```refcursor``` named ```cf_refcursor``` is declared before opening the ```BEGIN...END;``` block of the procedure, and returned before the end of this block. It is up to you to return another ```refcursor``` in your raw code.

If you need to declare more variables in the ```DECLARE...BEGIN``` block, you can do it through the **customVariables** attribute.

### Example of a load() raw function

This method loads all the instances of the Employee entity. It has no other interest than to demonstrate how to create a simple raw function:

```sql
LOAD() RAW
```

```
OPEN cf_refcursor FOR SELECT * FROM "public"."Employee";
```

In this case, since the return type is not customized, there is no need to return manually ```cf_refcursor``` at the end of the procedure. The ```RETURN``` statement will be added automatically by the producer.

## Differential engine (experimental)

The differential engine of the PostgreSQL Producer always **reconstructs** all the constraints, sequences, views and functions of the database. The differential logic is only executed on **schemas**, **tables** and **instances** that are either created or modified in the model. Instances are restored to the model value, had they been modified.

### Deleted columns

By default a column which exists in a table in the database but does not exist in the model is **automatically dropped** by the producer. This feature can be disabled in the configuration by setting the **Drop deleted properties** field to **False**.

![](img/postgresql-02.png)

### Deleted tables

If a table is defined in the databse but not in the model, it is **not dropped by default**. This means that relations tables belonging to deleted Many-To-Many relations will be kept although the matching relation has been deleted from the model. If you wish the differential engine to clean-up after you, set its **Drop unknown tables** property to **True**.

*Note: Unknown tables are not deleted by default as you might be working in a schema that already contains tables unrelated to your model. In such case activating this feature would cause all unknown tables to be deleted.*

### Type casts

When modifying the type of a property, the differential engine will automatically cast the existing column to the new type. If the **Explicit Casts** setting is set to **True** (which is the default value), the differential will use the SQL instruction ```CAST(sourcetype AS targetype)```. If you are intending to change a column's type to an incomptible type, you can create a specific cast procedure using the [CREATE CAST](http://www.postgresql.org/docs/8.4/static/sql-createcast.html) instruction.

### Sequences

Sequences of identity columns are automatically dropped and rebuilt by the differential engine at each generation.

## Target versions

Starting from **PostgreSQL 8.4**, all upper versions are supported. Default target version is **PostgreSQL 8.4**.

### Notes about PostgreSQL 8.4

PostgreSQL 8.4 does not support anonymous blocks (see the [DO](http://www.postgresql.org/docs/9.0/static/sql-do.html) instruction). All the PL/pgSQL scripts are imbricated in a function that is sequentially created, executed, and then dropped.

PostgreSQL 8.4 does not support [collation](http://www.postgresql.org/docs/9.1/static/collation.html).

### Notes about PostgreSQL 9.0

PostgreSQL 9.0 does not support [collation](http://www.postgresql.org/docs/9.1/static/collation.html).