# Use your Data Access Layer

## Create your application

Add a console application project into your solution and add a reference to your generated C# project.

![](img/use-your-dal-01.png)

Add a loop to display the list of your products.

```csharp
using System;
using OrderProcess.Marketing;

namespace OrderProcess.Application
{
    class Program
    {
        static void Main(string[] args)
        {
            foreach (Product product in ProductCollection.LoadAll())
            {
                Console.WriteLine(product.Name);
            }
            Console.ReadKey();
        }
    }
}
```


## Add instances

Now let's add some data to our model. Select the Product entity and open the Instances Grid windows from the ribbon.

![](img/use-your-dal-02.png)

Fill the grid with all the products you need to initialize your database.

![](img/use-your-dal-03.png)

Build your model and your database is now filled with your initial data.

![](img/use-your-dal-04.png)

## Run the application

Now run the application. It should display our three products.

![](img/use-your-dal-05.png)
