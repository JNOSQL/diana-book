# Column

The Column is a small piece of the column family entity. Each column has a tuple where the name represents a key and the value itself as a `Value` implementation.

```java
        Column document = Column.of("name", "value");
        Value value = document.getValue();
        String name = document.getName();
```

With this interface we may have a column inside a column.

```java
Column subColumn = Column.of("subColumn", column);
```

The way to storage subcolumn will also depend on each driver implementation as well as the information.

To access the information from `Column` it has an alias method to `Value`, thus you can do a conversion directly from `Column` _interface_.

```java
Column age = Column.of("age", 29);
String ageString = age.get(String.class);
List<Integer> ages = age.get(new TypeReference<List<Integer>>() {});
Object ageObject = age.get();
```

