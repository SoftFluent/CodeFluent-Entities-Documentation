# Extend your Data Access Layer

## Just standard .NET development

As you can see from the declaration of the **Product** class, every generated class is **partial**:

    public partial class Product : System.ICloneable, System.IComparable, System.IComparable<OrderProcess.Marketing.Product>, ...

If you need to add a property, you create a separated partial class, in a **Product.partial.cs** in our case, and put some logic in it:

    namespace OrderProcess.Marketing
    {
        public partial class Product
        {
            public bool IsUnavailable
            {
                get
                {
                    return !IsAvailable;
                }
            }
        }
    }

## Add a method

You can also add a business method to use in your application:

    public static void CreateFromProductBasket(ProductCollection products)
    {
        Order order = new Order();

        foreach (Product product in products)
        {
            order.Products.Add(product);
        }

        order.Status = OrderStatus.InProgress;
        order.Save();
    }