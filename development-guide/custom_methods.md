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

Using the **IN** operator you can exclude values that aren't included in a list. For instance:

```sql
LOAD
WHERE Title IN ('Root', 'Technical')
```

The **IN** operator can also be used with dynamic parameters:

LOAD(string value1, string value2)
WHERE Title IN (@value1, @value2)

Furthermore, if sending a string array as a parameter, the in parameter can be used to filter values by the passed array:

LOAD(string[] values)
WHERE Title IN (@values)

### Top

There isn't a TOP operator in CFQL, however top is supported on the Method property grid through the maxCount property.

![](img/custom-method-01.png)