# Application configuration

The **Business Object Model** (BOM) is the only layer which has a direct access to the data layer; hence you have to configure its connection settings to it. To do so, CodeFluent provides in the **CodeFluent.Runtime.dll** a configuration section handler. Consequently, all you have to do to configure your application's connection settings, is to create a configuration section of the BOM's namespace, and in this section, specify the connection string.

```xml
<configuration>
  <configSections>
    <section name="MyDefaultNamespace" type="CodeFluent.Runtime.CodeFluentConfigurationSectionHandler, CodeFluent.Runtime" />
  </configSections>
  <MyDefaultNamespace connectionString="database=[MyDatabaseName];server=[ServerName];Trusted_Connection=true" />
</configuration>
```

As you can see in the sample above:

1. Declare a new configuration section with a section name matching your default namespace,
2. Add a section corresponding to the declared name (your default namespace),
3. Set the connection string which the application will be using at runtime when interacting with the database layer.

By default CodeFluent Entities uses this connection string:

```Application Name=[DefaultNamespace];server=127.0.0.1;database=[DefaultNamespace];Integrated Security=true```

*Note: Each CodeFluent Entities model contains a project element, thus the project element has an attribute named defaultNamespace which must be set. By default, this is the value used as an application name and database name in the default connection string.*