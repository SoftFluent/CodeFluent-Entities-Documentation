# Use your Data Access Layer

## Create your application

Add a console application project into your solution and add a reference to your generated C# project.

![](img/getting-started/use-your-dal-01.png)

Add a loop to display the list of your products.

    using System;
    using OrderProcess.Marketing;
    
    namespace OrderProcess.Application
    {
        class Program
        {
            static void Main(string[] args)
            {
                foreach (Product product in ProductCollection.LoadByAvailable(true))
                {
                    Console.WriteLine(product.Name);
                }
                Console.ReadKey();
            }
        }
    }


## Add instances

Your application source code should be ready for display, now let's add some data into our model. Select the Product entity and open the Instances Grid windows from the ribbon.

![](img/getting-started/use-your-dal-02.png)

Fill the grid with all the products you need to initialize your database.

![](img/getting-started/use-your-dal-03.png)

Build your model and your database is now up to date.

![](img/getting-started/use-your-dal-04.png)

## Run the application

Now run the application. It should display our three products.
