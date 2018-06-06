# Template de Documentos

O template de documentos é responsável para realizar a comunicação da entidade para um banco de dados do tipo documentos. Ele é subdividido em `DocumentTemplate` e `DocumentTemplateAsync`para trabalhos síncronos e assíncronos respectivamente.

## `DocumentTemplate`

O `DocumentTemplate` é responsável pela persistência de uma Entidade em um banco de dados do tipo documento. Ele é composto, basicamente, por três componentes:

* **DocumentEntityConverter**: Responsável por converter da entidade, por exemplo, Person para DocumentEntity.
* **DocumentCollectionManager**: Entidade manager de documentos do Diana.
* **DocumentWorkflow**: Segue o fluxo de persistência durante os métodos de save e update.

```java
DocumentTemplate template = //instance

Person person = new Person();
person.setAddress("Olympus");
person.setName("Artemis Good");
person.setPhones(Arrays.asList("55 11 94320121", "55 11 94320121"));
person.setNickname("artemis");

List<Person> people = Collections.singletonList(person);

Person personUpdated = template.save(person);
template.insert(people);
template.insert(person, Duration.ofHours(1L));

template.update(person);
template.update(people);
```

Para a busca e a remoção da informação são utilizadas as mesmas classes do Diana para documentos, ou seja, **DocumentQuery** e **DocumentDeleteQuery** respectivamente.

```java
DocumentQuery query = select().from("Person").where("address").eq("Olympus").build();

List<Person> peopleWhoLiveOnOlympus = template.find(query);
Optional<Person> artemis = template.singleResult(select().from("Person")
                .where("nickname").eq("artemis").build());

DocumentDeleteQuery deleteQuery = delete().from("Person").where("address").eq("Olympus").build();
template.delete(deleteQuery);
```

Como o motor do Artemis é CDI para que se posso utilizar o DocumentTemplate basta dar um @Inject num campo.

```java
@Inject
private DocumentTemplate template;
```

Para isso é necessário que a aplicação injete um **DocumentCollectionManager:**

```java
@Produces
public DocumentCollectionManager getManager() {
    DocumentCollectionManager manager = //instance
    return manager;
}
```

Para trabalhar com mais de um tipo de TemplateTemplate existem duas opções:

1\) A primeira é com a utilização dos qualificadores:

```java
    @Inject
    @Database(value = DatabaseType.DOCUMENT, provider = "databaseA")
    private DocumentTemplate templateA;

    @Inject
    @Database(value = DatabaseType.DOCUMENT, provider = "databaseB")
    private DocumentTemplate templateB;


    //producers methods
    @Produces
    @Database(value = DatabaseType.DOCUMENT, provider = "databaseA")
    public DocumentCollectionManager getManagerA() {
        DocumentCollectionManager manager = //instance
        return manager;
    }

    @Produces
    @Database(value = DatabaseType.DOCUMENT, provider = "databaseB")
    public DocumentCollectionManager getManagerB() {
        DocumentCollectionManager manager = //instance
        return manager;
    }
```

2\) A segunda delas é a partir do **DocumentTemplateProducer**

```java
@Inject
private DocumentTemplateProducer producer;

public void sample() {
   DocumentCollectionManager managerA = //instance;
   DocumentCollectionManager managerB = //instance
   DocumentTemplate templateA = producer.get(managerA);
   DocumentTemplate templateB = producer.get(managerB);
}
```

## `DocumentTemplateAsync`

O`DocumentTemplateAsync`é responsável pela persistência de uma Entidade em um banco de dados do tipo documento de forma assíncrona. Ele é composto, basicamente, por dois componentes:

* **DocumentEntityConverter:** Responsável por converter da entidade, por exemplo, Person para DocumentEntity.
* **DocumentCollectionManagerAsync:** Entidade manager de documentos do Diana de forma assíncrona.

```java
DocumentTemplateAsync templateAsync = //instance

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

Para a busca e a remoção da informação são utilizadas as mesmas classes do Diana para documentos, ou seja, **DocumentQuery** e **DocumentDeleteQuery** respectivamente também é possível o uso de callback.

```java
Consumer<List<Person>> callBackPeople = p -> {};
Consumer<Void> voidCallBack = v ->{};
templateAsync.select(query, callBackPeople);
templateAsync.delete(deleteQuery);
templateAsync.delete(deleteQuery, voidCallBack);
```

Como o motor do Artemis é CDI para que se posso utilizar o DocumentTemplate basta dar um @Inject num campo.

```java
@Inject
private
DocumentTemplateAsync template;
```

Para isso é necessário que a aplicação injete um **DocumentCollectionManagerAsync:**

```text
@Produces
public DocumentCollectionManagerAsync getManager() {
    DocumentCollectionManagerAsync managerAsync = //instance
    return manager;
}
```

Para trabalhar com mais de um tipo de DocumentTemplate existem duas opções:

1\) A primeira é com a utilização dos qualificadores:

```java
    @Inject
    @Database(value = DatabaseType.DOCUMENT, provider = "databaseA")
    private DocumentTemplateAsync templateA;

    @Inject
    @Database(value = DatabaseType.DOCUMENT, provider = "databaseB")
    private DocumentTemplateAsync templateB;


    //producers methods
    @Produces
    @Database(value = DatabaseType.DOCUMENT, provider = "databaseA")
    public DocumentCollectionManagerAsync getManagerA() {
        DocumentCollectionManager manager = //instance
        return manager;
    }

    @Produces
    @Database(value = DatabaseType.DOCUMENT, provider = "databaseB")
    public DocumentCollectionManagerAsync getManagerB() {
        DocumentCollectionManager manager = //instance
        return manager;
    }
```

2\) A segunda delas é a partir do **DocumentTemplateAsyncProducer**

```java
@Inject
private DocumentTemplateAsyncProducer producer;

public void sample() {
   DocumentCollectionManagerAsync managerA = //instance;
   DocumentCollectionManagerAsync managerB = //instance
   DocumentTemplateAsync templateA = producer.get(managerA);
   DocumentTemplateAsync templateB = producer.get(managerB);
}
```

