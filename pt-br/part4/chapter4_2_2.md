# chapter4\_2\_2

O template de família de coluna é responsável para realizar a comunicação da entidade para um banco de dados do tipo família de coluna. Ele é subdividido em `ColumnTemplate` e `ColumnTemplateAsync` para trabalhos síncronos e assíncronos respectivamente.

## ColumnTemplate

O ColumnTemplate é responsável pela persistência de uma Entidade em um banco de dados do tipo coluna. Ele é composto, basicamente, por três componentes:

* **ColumnEntityConverter**: Responsável por converter da entidade, por exemplo, Person para ColumnEntity.
* **ColumnCollectionManager**: Entidade manager de documentos do Diana.
* **ColumnWorkflow**: Segue o fluxo de persistência durante os métodos de save e update.

```java
ColumnTemplate template = //instance

Person person = new Person();
person.setAddress("Olympus");
person.setName("Artemis Good");
person.setPhones(Arrays.asList("55 11 94320121", "55 11 94320121"));
person.setNickname("artemis");

List<Person> people = Collections.singletonList(person);

Person personUpdated = template.insert(person);
template.insert(people);
template.insert(person, Duration.ofHours(1L));

template.update(person);
template.update(people);
```

Para a busca e a remoção da informação são utilizadas as mesmas classes do Diana para documentos, ou seja, **ColumnQuery** e **ColumnDeleteQuery** respectivamente.

```java
ColumnQuery query = select().from("Person").where("address").eq("Olympus").build()

List<Person> peopleWhoLiveOnOlympus = template.select(query);
Optional<Person> artemis = template.singleResult(select().from("Person").where("nickname")
                .eq("artemis").build());

ColumnDeleteQuery deleteQuery = delete().from("Person").where("address").eq("Olympus").build()
template.delete(deleteQuery);
```

Como o motor do Artemis é CDI para que se posso utilizar o ColumnTemplate basta dar um @Inject num campo.

```java
@Inject
private ColumnTemplate template;
```

Para isso é necessário que a aplicação injete um **ColumnFamilyManager**:

```java
@Produces
public ColumnFamilyManager getManager() {
    ColumnFamilyManager manager = //instance
    return manager;
}
```

Para trabalhar com mais de um tipo de ColumnTemplate existem duas opções:

1\) A primeira é com a utilização dos qualificadores:

```java
    @Inject
    @Database(value = DatabaseType.COLUMN, provider = "databaseA")
    private ColumnTemplate templateA;

    @Inject
    @Database(value = DatabaseType.COLUMN, provider = "databaseB")
    private ColumnTemplate templateB;


    //producers methods
    @Produces
    @Database(value = DatabaseType.COLUMN, provider = "databaseA")
    public ColumnFamilyManager getManagerA() {
        ColumnFamilyManager manager =//instance
        return manager;
    }

    @Produces
    @Database(value = DatabaseType.COLUMN, provider = "databaseB")
    public ColumnFamilyManager getManagerB() {
        ColumnFamilyManager manager = //instance
        return manager;
    }
```

2\) A segunda delas é a partir do **ColumnTemplateProducer**

```java
@Inject
private ColumnTemplateProducer producer;

public void sample() {
   ColumnFamilyManager managerA = //instance;
   ColumnFamilyManager managerB = //instance
   ColumnTemplate templateA = producer.get(managerA);
   ColumnTemplate templateB = producer.get(managerB);
}
```

## ColumnTemplateAsync

O `ColumnTemplateAsync` é responsável pela persistência de uma Entidade em um banco de dados do tipo família de colunas de forma assíncrona. Ele é composto, basicamente, por dois componentes:

* **ColumnEntityConverter:** Responsável por converter da entidade, por exemplo, Person para ColumnEntity.
* **ColumnFamilyManagerAsync:** Entidade manager de documentos do Diana de forma assíncrona.

```java
ColumnTemplateAsync templateAsync = //instance

Person person = new Person();
person.setAddress("Olympus");
person.setName("Artemis Good");
person.setPhones(Arrays.asList("55 11 94320121", "55 11 94320121"));
person.setNickname("artemis");

List<Person> people = Collections.singletonList(person);

Consumer<Person> callback = p -> {};
templateAsync.insert(person);
templateAsync.insert(person, Duration.ofHours(1L));
templateAsync.insert(person, callback);
templateAsync.insert(people);

templateAsync.update(person);
templateAsync.update(person, callback);
templateAsync.update(people);
```

Para a busca e a remoção da informação são utilizadas as mesmas classes do Diana para documentos, ou seja, **ColumnQuery** e **ColumnDeleteQuery** respectivamente também é possível o uso de callback.

```java
Consumer<List<Person>> callBackPeople = p -> {};
Consumer<Void> voidCallBack = v ->{};
templateAsync.select(query, callBackPeople);
templateAsync.delete(deleteQuery);
templateAsync.delete(deleteQuery, voidCallBack);
```

Como o motor do Artemis é CDI para que se posso utilizar o ColumnTemplate basta dar um @Inject num campo.

```java
@Inject
private ColumnTemplateAsync template;
```

Para isso é necessário que a aplicação injete um **ColumnFamilyManagerAsync:**

```text
@Produces
public ColumnFamilyManagerAsync getManager() {
    ColumnFamilyManagerAsync managerAsync = //instance
    return manager;
}
```

Para trabalhar com mais de um tipo de ColumnTemplateAsync existem duas opções:

1\) A primeira é com a utilização dos qualificadores:

```java
    @Inject
    @Database(value = DatabaseType.COLUMN, provider = "databaseA")
    private ColumnTemplateAsync templateA;

    @Inject
    @Database(value = DatabaseType.COLUMN, provider = "databaseB")
    private ColumnTemplateAsync templateB;


    //producers methods
    @Produces
    @Database(value = DatabaseType.COLUMN, provider = "databaseA")
    public ColumnFamilyManagerAsync getManagerA() {
        ColumnFamilyManagerAsync manager = //instance
        return manager;
    }

    @Produces
    @Database(value = DatabaseType.COLUMN, provider = "databaseB")
    public ColumnFamilyManagerAsync getManagerB() {
        ColumnFamilyManagerAsync manager = //instance
        return manager;
    }
```

2\) A segunda delas é a partir do **ColumnTemplateAsyncProducer**

```java
@Inject
private DocumentTemplateAsyncProducer producer;

public void sample() {
   ColumnFamilyManagerAsync managerA = //instance;
   ColumnFamilyManagerAsync managerB = //instance
   ColumnTemplateAsync templateA = producer.get(managerA);
   ColumnTemplateAsync templateB = producer.get(managerB);
}
```

