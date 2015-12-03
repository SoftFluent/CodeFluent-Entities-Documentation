# Update your model

## Add a property

Let's add a new "IsAvailable" boolean property on the Product entity. Then edit the Instances Grid to set the available products.

![](img/getting-started/update-your-model-01.png)

Have a look at the **tables_diffs.sql** script in the SQL Server database project. You can see this generated instruction:

    /* column 'Product_IsAvailable' was not found in table 'Product'. */
    ALTER TABLE [dbo].[Product] ADD [Product_IsAvailable] [bit] NULL

And your instances are also up to date:

![](img/getting-started/update-your-model-02.png)


## Add a method

We are going to add a new Load method to our Product entity that use this new property.

![](img/getting-started/update-your-model-03.png)
![](img/getting-started/update-your-model-04.png)

We will write this method in CFQL (CodeFluent Query Language) to generate the associated stored procedure.

![](img/getting-started/update-your-model-05.png)

CFQL
show generated SP

## Update your application

Edit Load method used
Run
