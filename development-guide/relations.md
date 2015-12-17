# Relations

## Relations

The BOM is a direct implementation of the model, consequently it applies the structural rules defined by the model. From the model we understand that:

* a customer can have several orders,
* an order can have only one customer,
* an order can contain several products.

The generated BOM conforms to those structural rules, see how to navigate in the BOM:

```csharp
Customer customer = customer.Load(42);
foreach (Order order in customer.Orders)
{
        Console.WriteLine("Order #" + order.Id + " contains:");
        foreach (Product product in order.Products)
        {
            Console.WriteLine(" + Product #" + product.Id + ": " + product.Label);
        }
}
```

As you can see relation properties are ```lazy loaded``` meaning that when getting the ```customer.Orders``` properties, if it's null, it's value will automatically be loaded from the database. Next times, as the property is non null, no extra round trip to the database will be done.

Creating relations is handled automatically:

```csharp
Customer customer = Customer.Load(42);
Product product = product.Load(1);
 
Order order = new Order();
order.Reference = Guid.NewGuid().ToString();
order.Status = OrderStatus.InProgress;
order.Customer = customer;
order.Products.Add(product);
order.Save();
```

The here-above code snippet creates an order the customer of id 42, which contains the product of id 1. Later in the code, if ever we iterate through the customer 42's orders, will see that it will contain the created order.