# Extend your Data Access Layer

## Just standard .NET development

As you can see from the declaration of the **Order** class, every generated class is **partial**:

```csharp
    public partial class Order
```

If you need to add a method, you create a separated partial class, in a **Order.partial.cs** file in our case, and put some logic in it:

```csharp
    using OrderProcess.Sales;
    using OrderProcess.Marketing;
    
    namespace OrderProcess.Sales
    {
        public partial class Order
        {
            public static Order CreateFromProductBasket(ProductCollection products)
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