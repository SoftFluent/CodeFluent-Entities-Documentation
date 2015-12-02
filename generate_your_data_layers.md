# Generate your data layers

You will need to create Visual Studio projects to host your generated source code:
* C# class library project
* SQL Server database project

Then you can add a "Producer" (the term  we use for "code generator") to generate the persistence layer.

![](img/getting-started/generate-your-data-layers-01.png)

Configure the connectionString and target the SQL Server database project.

![](img/getting-started/generate-your-data-layers-03.png)

![](img/getting-started/generate-your-data-layers-02.png)

