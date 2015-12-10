# Custom methods

## CFQL

CFQL ("CodeFluent Query Language") supports operators such as:

* unary operators: not, exists
* binary operators: and, or, equals, contains, freetext, like, =, <>, >=, <=, >, <
* set operator: in (expression1, expression2, ... , expressionM)


### Unary Operator

### Equality and Inequality Operators

### String Operators

### Set Operator

Using the **in** operator you can exclude values that aren't included in a list. For instance:

```load where Title in ('Root', 'Technical')```

### Top

There isn't a TOP operator in CFQL, however top is supported on the Method property grid through the maxCount property.

![](img/custom-method-01.png)