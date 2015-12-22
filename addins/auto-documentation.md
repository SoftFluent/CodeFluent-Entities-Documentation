# Auto-Documentation

CodeFluent Entities provides a documentation sub-producer which allows you to:
* Decorate all members with XML comments (usable by Visual Studio IntelliSense and help tools),
* Generate XML, batch and configuration files to create a .CHM documentation using Sandcastle.

To benefit from it, you first need to add an instance of the sub-producer. To do so, select the Business Object Model Producer instance of your project, right-click on it and select “Add New SubProducer”:

![](img/auto-trace-01.png)

Then in the “Add New SubProducer” dialog, select “Documentation” in the tree view and configure it: