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