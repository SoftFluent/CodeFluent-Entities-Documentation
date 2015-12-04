# Extend your Data Access Layer

## Just standard .NET development

As you can see from the declaration of the **Product** class, every generated class is **partial**:

```csharp
    public partial class Product
```

If you need to add a property, you create a separated partial class, in a **Product.partial.cs** in our case, and put some logic in it:

```csharp
    using OrderProcess.Sales;
    
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
        }
    }
```

This way you avoid using too much layers or wrappers to separate the generated classes from the developed ones.

Your generated classes are business objects, not just data access ones.