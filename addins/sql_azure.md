# SQL Azure

The SQL Azure producer translates your model into SQL Azure compliant T-SQL scripts to generate the equivalent SQL Azure database. Moreover the generated scripts can be run automatically on the SQL Azure database.

*Note: Unlike the SQL Server Producer, this producer doesn't create the SQL Azure database. On SQL Azure, billing is based, in part, on your database edition and size. Therefore, we're letting developers create their database as desired, and the tool focuses on generating the database schema (schemas, tables, views, stored procedures, constraints, data, etc.).*

Another key feature of the SQL Azure producer, is that you can work locally, by generating on a local SQL Server database. In this scenario, the SQL Azure producer will generate 100% compliant code on both platforms. Furthermore, when generating on a SQL Server instance, the producer detects it and will use a **diff engine** which will update the database through generations rather than dropping and creating it over.

This diff engine is in fact a key feature since it allows developers to generate continuously, without ever losing data. Once the application is ready to go live, the developer can configure the producer to run the scripts on the online SQL Azure database.

*Using CodeFluent Entities versions **prior to** 60809.671, the Diff Engine uses SQL Distributed Management Objects (SQL-DMO), therefore, if using SQL Server 2008 or SQL Server 2008 R2 as your local server, you'll need to install this component on your server to benefit from this feature.*

*You can download them here (only the “Microsoft SQL Server 2005 Backward Compatibility Components” is needed): http://www.microsoft.com/downloads/en/details.aspx?FamilyId=C6C3E9EF-BA29-4A43-8D69-A2BED18FE73C&displaylang=en.*

## Using the producer

![](img/sql-azure-01.png)

As you can see the producer configuration is composed of two sections:

* local settings,
* online settings

The **Produce Scripts** property of the two sections (true for **local** and false for **online** by default) control which of the settings are used.

If **Produce Scripts** is true for the **local** section, the producer generates the scripts in the directory pointed by the **Target Directory** path of the **local** section, and runs them on the database pointed by the **Connection String** of the **local** section, if the **Run Scripts** (true by default) is set to true in the **local** section.

By clicking on the Advanced button (the one with the yellow 'plus' sign at the top of the property grid), you'll access extra options, common to both settings (online and local) allowing you to control the script generation. Among available options are wether to produce views, schemas, the file encoding, etc.

SQL Azure being a cloud version of SQL Server, you'll find that except this dual setting to support local and online scenarios, features (namespace, entities, relations, views, methods, etc.), most options (Produce Views, Produce Schemas, etc.) and behaviors (script generation and execution), are the same between both producers.

## Windows Azure Blob Storage

Starting with the build 579, the CodeFluent Entities runtime supports the Windows Azure Blob Storage to store CodeFluent Entities binary large objects. 

It means the developer can choose at runtime where the blobs and large files are going to be saved. It is completely independent from the design or modeling phase of your CodeFluent Entities project, and completely independent from the persistence producers used at generation time.

### Prerequisites

The developer needs to copy two assemblies in the .NET bin execution path (or simply reference them in the .NET project being developed):

* **CodeFluent.Runtime.Azure.dll**: this assembly is shipped with CodeFluent Entities.
* **Microsoft.WindowsAzure.StorageClient.dll**: this assembly is provided with the Windows Azure SDK. It is generally found in the Program Files\Windows Azure SDK\vX.X\bin directory.

**Windows Azure** is a completely different platform than **SQL Azure**. If you want to minimize the cost of blob storage and data access transactions, it is probably better to store large files and blobs in the Blob Storage environment available with Windows Azure.

### Runtime Configuration

All you have to do to switch to the Windows Azure Blob Storage, is to modify the configuration section of the BOM's namespace as described here:

```xml
<configuration>
 <configSections>
   <section name="MyDefaultNamespace" type="CodeFluent.Runtime.CodeFluentConfigurationSectionHandler, CodeFluent.Runtime" />
 </configSections>
 <MyDefaultNamespace ... other attributes ...
  binaryServicesTypeName="azure"
  cloudStorageAccountConnectionString="DefaultEndpointsProtocol=http;AccountName=myAccount01;AccountKey=DGP2vZXD95DGHcCZRAfzfbkKCV0943EvBIRCv7HEGiQ=="
  />
</configuration>
```

The **binaryServicesTypeName** 'azure' value is in fact a shortcut to **CodeFluent.Runtime.Azure.AzureBinaryServicesEntityConfiguration, CodeFluent.Runtime.Azure**. It instructs the CodeFluent Entities runtime to use the **AzureBinaryLargeObject** provided type to manage CodeFluent Entities blobs low level operations.

The **cloudStorageAccountConnectionString** attribute must contain your Windows Azure Cloud Storage connection string. If this attribute is unspecified or left empty, **which is the recommended setting in the development and testing phase**, the system will use the **UseDevelopmentStorage=true** value, which means blobs will be stored in your local Windows Azure Development Storage.

###Per-Entity Runtime Configuration

By default, all blobs for all entities in the project will be stored the same way. However, it is possible to define a different blob storage mode for each entity in the project. Note all blobs properties in a given entity will be stored the same way.

All you have to do is to modify the configuration section like this:

```xml
<configuration>
 <configSections>
   <section name="MyDefaultNamespace" type="CodeFluent.Runtime.CodeFluentConfigurationSectionHandler, CodeFluent.Runtime" />
 </configSections>
 <MyDefaultNamespace ... other attributes ... >
   <binaryServices entityFullTypeName="MyDefaultNamespace.MyNamespace.MyEntity"
     binaryServicesTypeName="azure"
     cloudStorageAccountConnectionString="DefaultEndpointsProtocol=http;AccountName=myAccount01;AccountKey=vZXD95DGHcCZR=="
    />
   <binaryServices entityFullTypeName="MyDefaultNamespace.MyNamespace.MyOtherEntity"
     binaryServicesTypeName="azure"
     cloudStorageAccountConnectionString="DefaultEndpointsProtocol=https;AccountName=myAccount02;AccountKey=vZXD95Dzc5GHcCZR=="
    />
 </MyDefaultNamespace>
</configuration>
```

### Blob Storage Layout

By default, blobs for a given CodeFluent Entities model are created in an Azure blob container named 'cf-<default namespace>' (with respect for strict containers naming convention). This container will be created automatically.

In the container, a blob name has the following format, using the ```\``` character will allows easy navigation when using Azure Browsing tools (such as **Cloudberry Explorer for Azure Blob Storage**): ```<Entity Full Type Name>\<EntityName>_<Property Name>\<Concatenated list of ids>```.

Examples:

* MyDefaultNamespace.MyNamespace.MyEntity\MyEntity_Photo\53
* MyDefaultNamespace.MyNamespace.MyOtherEntity\MyOtherEntity_BlobProp\28|52

In this example, blobs are stored with the default configuration for all entities, but in Windows Azure Blob Storage for the MyEntity and MyOtherEntity entities. As an example, each entity defines a different set of credentials.

For a more thorough discussion on this subject, we suggest you to check out this MSDN article: [SQL Azure and Windows Azure Table Storage](https://msdn.microsoft.com/en-gb/magazine/gg309178.aspx)