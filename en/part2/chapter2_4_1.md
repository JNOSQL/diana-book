# Document Manager

The manager class to a document type can be synchronous or asynchronous:

* **DocumentCollectionManager**: To do synchronous operations.
* **DocumentCollectionManagerAsync**: To do asynchronous operations.

## **DocumentCollectionManager**

The `DocumentCollectionManager` is the class that manages the persistence on the synchronous way to document collection.

```java
        DocumentEntity entity = DocumentEntity.of("collection");
        Document diana = Document.of("name", "Diana");
        entity.add(diana);

        List<DocumentEntity> entities = Collections.singletonList(entity);
        DocumentCollectionManager manager = //instance;

        //insert operations
        manager.insert(entity);
        manager.insert(entity, Duration.ofHours(2L));//inserts with 2 hours of TTL
        manager.insert(entities, Duration.ofHours(2L));//inserts with 2 hours of TTL
        //updates operations
        manager.update(entity);
        manager.update(entities);
```

## **DocumentCollectionManagerAsync**

The `DocumentCollectionManagerAsync` is the class that manages the persistence on an asynchronous way to document collection.

```java
        DocumentEntity entity = DocumentEntity.of("collection");
        Document diana = Document.of("name", "Diana");
        entity.add(diana);

        List<DocumentEntity> entities = Collections.singletonList(entity);
         DocumentCollectionManagerAsync managerAsync = //instance

        //insert operations
        managerAsync.insert(entity);
        managerAsync.insert(entity, Duration.ofHours(2L));//inserts with 2 hours of TTL
        managerAsync.insert(entities, Duration.ofHours(2L));//inserts with 2 hours of TTL
        //updates operations
        managerAsync.update(entity);
        managerAsync.update(entities);
```

Sometimes on an asynchronous process, is important to know when this process is over, so the `DocumentCollectionManagerAsync` also has callback support.

```java
        Consumer<DocumentEntity> callBack = e -> {};
        managerAsync.insert(entity, callBack);
        managerAsync.update(entity, callBack);
```

## Search information on a document collection

Diana has support to retrieve information from both ways synchronous and asynchronous from the `DocumentQuery` class. The `DocumentQuery` has information such as sort type, document and also the condition to retrieve information.

The condition on `DocumentQuery` is given from `DocumentCondition`, which has the status and the document. Eg. The condition behind is to find a name equal "**Ada**".

```java
DocumentCondition nameEqualsAda = DocumentCondition.eq(Document.of("name", “Ada”));
```

Also, the developer can use the aggregators such as **AND**, **OR** e **NOT**.

```java
DocumentCondition nameEqualsAda = DocumentCondition.eq(Document.of("name", "Ada"));
DocumentCondition youngerThan2Years = DocumentCondition.lt(Document.of("age", 2));
DocumentCondition condition = nameEqualsAda.and(youngerThan2Years);
DocumentCondition nameNotEqualsAda = nameEqualsAda.negate();
```

If there isn't a condition in the query that means the query will try to retrieve all information from the database, similar to a “`select * from database`” in a relational database, just remembering that the return depends on the driver. It is important to say that not all NoSQL databases have support for this resource.

DocumentQuery also has pagination feature to define where the data start, and it limits.

```java
DocumentCollectionManager manager = //instance;
DocumentCollectionManagerAsync managerAsync = //instance;
DocumentQuery query = DocumentQueryBuilder.select().from("collection").where("age")
                .lt(10).and("name").eq("Ada").orderBy(Sort.of("name", ASC)).limit(10).start(2).build();
List<DocumentEntity> entities = manager.select(query);
Optional<DocumentEntity> entity = manager.singleResult(query);
Consumer<List<DocumentEntity>> callback = e -> {};
managerAsync.select(query, callback);
```

## Removing information from Document Collection

Such as `DocumentQuery` there is a class to remove information from the document database type: A `DocumentDeleteQuery` type.

It is smoother than `DocumentQuery` because there isn't pagination and sort feature, once this information is unnecessary to remove information from database.

```java
        DocumentCollectionManager manager = //instance;
        DocumentCollectionManagerAsync managerAsync = //instance;

        DocumentDeleteQuery query = DocumentQueryBuilder.delete().from("collection").where("age").gt(10)
                .build();


        manager.delete(query);

        managerAsync.delete(query);
        managerAsync.delete(query, v -> {});
```

