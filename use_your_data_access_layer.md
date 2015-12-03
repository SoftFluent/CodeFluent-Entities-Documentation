# Use your Data Access Layer

## Create your application

Add a console application project into your solution and add a reference to your generated C# project.



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



## Run the application

