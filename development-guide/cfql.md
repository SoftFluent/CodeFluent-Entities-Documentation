# CFQL

CFQL ("CodeFluent Query Language") supports operators such as:

* unary operators: not, exists
* binary operators: and, or, equals, contains, freetext, like, =, <>, >=, <=, >, <
* set operator: in (expression1, expression2, ... , expressionM)


### Unary Operator

### Equality and Inequality Operators

Equality and inequality operators (```=```, ```<>```, ```>=```, ```<=```, ```>```, ```<```) allow you to compare values between one another.

Here's a set of examples:

```sql
LOAD
WHERE Position = 0
```

LoadNonFirsts::
```sql
LOAD
WHERE Position <> 0
```

LoadCurrentAndPrevious:
```sql
LOAD(Position)
WHERE Position <= @Position
```

LoadPrevious:
```sql
LOAD(Position)
WHERE Position < @Position
```

LoadCurrentAndNext:
```sql
LOAD(Position)
WHERE Position >= @Position
```

LoadNext:
```sql
LOAD(Position)
WHERE Position > @Position
```

### String Operators

Available operators are: ```EQUALS``` (```=``` is also supported), ```CONTAINS```, ```FREETEXT``` and ```LIKE``` (```IS LIKE```, ```STARTS WITH```, ```STARTSWITH``` and ```#``` are also supported).

```sql
LOAD(Title)
WHERE Title = @Title
```

```sql
LOAD(Title)
WHERE Title LIKE @Title
```

```sql
LOAD(string token)
WHERE Description CONTAINS @token
```

```sql
LOAD(string token)
WHERE Description FREETEXT @token
```

*Note: Desired column must be have a **Full-Text index** defined to be used with the ```FREETEXT``` and ```CONTAINS``` operators.*

### Set Operator

Using the ```IN``` operator you can exclude values that aren't included in a list. For instance:

```sql
LOAD
WHERE Title IN ('Root', 'Technical')
```

The ```IN``` operator can also be used with dynamic parameters:

```sql
LOAD(string value1, string value2)
WHERE Title IN (@value1, @value2)
```

Furthermore, if sending a string array as a parameter, the in parameter can be used to filter values by the passed array:

```sql
LOAD(string[] values)
WHERE Title IN (@values)
```

*Note: the previous method uses an out-of-the-box feature of the **SQL Server Producer**. Therefore, as of today, it's not supported by other persistence producers.*

### Top

There isn't a ```TOP``` operator in CFQL, however top is supported on the **Method** property grid through the **Maximum Count** property.

![](img/cfql-01.png)