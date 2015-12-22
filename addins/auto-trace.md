# Auto-Trace

The **Automatic Traces Producer** is a sub-producer of the [Business Object Model Producer](../code-generators/c_business_object_model_generator.md), which modifies its output by automatically adding traces to the generated code.

## Configuration

In the **Solution Explorer**, right-click on your **Business Object Model Producer** and select **Add New SubProducer**:

![](img/auto-trace-01.png)

Select **Automatic Traces** amongst proposed producers and click OK:

![](img/auto-trace-02.png)

Generating your model over again will automatically add traces to your code.

## Result