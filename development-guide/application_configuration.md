# Application configuration

## Connection string

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

*Note: Each CodeFluent Entities model contains a **project** element, thus the project element has an attribute named **defaultNamespace** which must be set. By default, this is the value used as an application name and database name in the default connection string.*

*Note: CodeFluent uses the environment variable named **CF_DEFAULT_PERSISTENCE_SERVER** as the default server. If ever this environment variable isn't set, the default value **127.0.0.1** is used. If ever an explicit connection string is specified, this value isn't used.*

You can retrieve the current connection string at runtime using the following code:

```csharp
CodeFluent.Runtime.CodeFluentPersistence persistence = CodeFluentContext.Get([DefaultNamespace].Constants.[DefaultNamespace]StoreName).Persistence;
System.Console.WriteLine(persistence.ConnectionString);
```

## Sharing connection strings

As explained in the previous paragraph, the BOM needs connection string to know where to retrieve its data. If using a connection string other than the default one, it must be configured in its specific configuration section. However, if ever you're using other .NET components that also need the connection string, having it declared in a custom section implies that you will have to duplicate it.

To avoid such problems, the CodeFluent runtime also supports declaring it in the standard **connectionStrings** section and referring to it using the connection string name:

```xml
<configuration>
  <configSections>
    <section name="Sample" type="CodeFluent.Runtime.CodeFluentConfigurationSectionHandler, CodeFluent.Runtime"/>
  </configSections>
  <connectionStrings>
    <add name="SqlServer" connectionString="Application Name=Sample;server=MYSERVER;database=Sample;Integrated Security=true" />
    <add name="SqlServerExpress" connectionString="Application Name=Sample;server=MYSERVER\SQLEXPRESS;database=Sample;Integrated Security=true" />
  </connectionStrings>
  <Sample connectionString="{SqlServer}" />
</configuration>
```

As you can see in the example above, several connection strings can be configured in the standard connectionStrings section, and then using the {ConnectionStringName} format, the runtime will use the one corresponding to the specified name.

More than making the connection string available to other .NET components, it allows you to keep all your connection strings (development, test, production, etc.) in the configuration file. Consequently, this will ease the configuration of your application on several environments.

## Combining ConnectionString Aliases with Environment Variables

Combined with the support of environment variables, this feature can be used to select a connection string automatically. For instance, **COMPUTERNAME** is an environment variable defined by default on machines. Using this variable as an alias to a connection string, we ensure the appropriate connection string will automatically be used depending on the computer from which the application is ran.