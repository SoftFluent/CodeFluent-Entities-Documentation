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

## Using the PostgreSQL Producer

The PostgreSQL Producer is implemented by the **CodeFluent.Producers.PostgreSQLProducer** class, contained in the **CodeFluent.Producers.PostgreSQL** assembly.

### Generating

The PostgreSQL Producer is available in the **Persistence Layer Producers** section of the **Add New Producer** window.



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