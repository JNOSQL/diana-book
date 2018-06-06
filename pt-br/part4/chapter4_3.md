# CrudRepository

Além dos repositórios de família de colunas e também de documentos o Artemis também possui o Repository. Essa interface tem como objetivo auxiliar na criação de classes repositórios específicas para as entidades além de facilitar na criação de uma query.

Para utilizar esse recurso é necessário apenas criar uma interface que extenda de **Repository**.

```java
    interface PersonRepository extends Repository<Person, String> {

    }
```

E injete o recurso, é necessário definir também o qualificador Database que será responsável por dizer para qual tipo de banco a informação será enviada.

```java
@Inject
@Database(DatabaseType.DOCUMENT)
private PersonRepository documentRepository;
@Inject
@Database(DatabaseType.COLUMN)
private PersonRepository columnRepository;
```

Para isso é necessário que a aplicação injete um ColumnFamilyManager ou um DocumentCollectionManager:

```java
@Produces
public DocumentCollectionManager getManager() {
DocumentCollectionManager manager = //instance
return manager;
}

@Produces
public ColumnFamilyManager getManager() {
ColumnFamilyManager manager = //instance
return manager;
}
```

Caso seja necessário trabalhar mais de um banco de dados, basta utilizar o qualificador Database e ele será elegível para injeção.

```java
@Inject
@Database(value = DatabaseType.DOCUMENT , provider = "databaseA")
private PersonRepository documentRepositoryA;

@Inject
@Database(value = DatabaseType.DOCUMENT , provider = "databaseB")
private PersonRepository documentRepositoryB;

@Inject
@Database(value = DatabaseType.COLUMN, provider = "databaseA")
private PersonRepository columnRepositoryA;

@Inject
@Database(value = DatabaseType.COLUMN, provider = "databaseB")
private PersonRepository columnRepositoryB;


//producers methods
@Produces
@Database(value = DatabaseType.COLUMN, provider = "databaseA")
public ColumnFamilyManager getColumnFamilyManagerA() {
ColumnFamilyManager manager =//instance
return manager;
}

@Produces
@Database(value = DatabaseType.COLUMN, provider = "databaseB")
public ColumnFamilyManager getColumnFamilyManagerB() {
ColumnFamilyManager manager = //instance
return manager;
}

@Produces
@Database(value = DatabaseType.DOCUMENT, provider = "databaseA")
public DocumentCollectionManager getDocumentCollectionManagerA() {
DocumentCollectionManager manager = //instance
return manager;
}

@Produces
@Database(value = DatabaseType.DOCUMENT, provider = "databaseB")
public DocumentCollectionManager DocumentCollectionManagerB() {
DocumentCollectionManager manager = //instance
return manager;
}
```

Com isso o Artemis se encarregará de utilizar o apropriado banco de dados e cuidará de implementar os métodos.

```java
PersonRepository repository = //instance

Person person = new Person();
person.setNickname("diana");
person.setName("Diana Goodness");

List<Person> people = Collections.singletonList(person);

repository.save(person);
repository.save(people);
```

## Criando Queries com o Repository

Além de salvar e atualizar a informação também é possível recuperar e deletar a informação utilizando methods queries. Com esse intuito o Repository vem com algumas palavras reservadas:

* **findBy**: Como prefixo para encontrar alguma informação
* **deleteBy**: Como prefixo, para deletar alguma informação

Além dos Operadores:

* And
* Or
* Between
* LessThan
* GreaterThan
* LessThanEqual
* GreaterThanEqual
* Like
* OrderBy
* OrderBy\_\_\_\_Desc
* OrderBy\_\_\_\_\_ASC

```java
interface PersonRepository extends Repository<Person, Long> {

    List<Person> findByAddress(String address);

    Stream<Person> findByName(String name);

    Stream<Person> findByNameOrderByNameAsc(String name);

    Optional<Person> findByNickname(String nickname);

    void deleteByNickName(String nickname);
}
```

Com isso o artemis cuidará de implementar esses métodos.

## Utilizando o Repository de forma assíncrona

Para trabalhar de forma assíncrona existe a interface RepositoryAsync, seu funcionamento é semelhante ao Repository.

```java
@Inject
@Database(DatabaseType.DOCUMENT)
private PersonRepositoryAsync documentRepositoryAsync;

@Inject
@Database(DatabaseType.COLUMN)
private PersonRepositoryAsync columnRepositoryAsync;
```

Ou seja, basta injetá-lo que o Artemis cuidará de implementar os métodos.

```java
PersonRepositoryAsync repositoryAsync = //instance

Person person = new Person();
person.setNickname("diana");
person.setName("Diana Goodness");

List<Person> people = Collections.singletonList(person);


repositoryAsync.save(person);
repositoryAsync.save(people);
```

Também é possível recuperar e deletar a informação de forma assíncrona, a diferença é que na recuperação um callback é obrigatório no fim do método enquanto para deletar ou remover informação o callback é opcional.

```java
    interface PersonRepositoryAsync extends RepositoryAsync<Person, Long> {

        void findByNickname(String nickname, Consumer<List<Person>> callback);

        void deleteByNickName(String nickname);

        void deleteByNickName(String nickname, Consumer<Void> callback);
    }
```

## Repository no KeyValue

Assim como a família de colunas e coleção de documentos, chave valor tem o recurso que auxilia tem como objetivo auxiliar na criação de classes repositórios.

Para utilizar esse recurso é necessário apenas criar uma interface que extenda de **Repository**.

```java
public interface UserRepository extends Repository<User, String> {
}
```

E injete o recurso.

```java
@Inject
private UserRepository userRepository;
```

Para isso basta produzir um BucketManager.

```java
@Produces
public BucketManager getManager() {
BucketManager manager =//instance
return manager;
}
```

Caso seja necessário trabalhar mais de um banco de dados, basta utilizar o qualificador Database e ele será elegível para injeção.

```java
@Inject
@Database(value = DatabaseType.KEY_VALUE, provider = "databaseA")
private UserRepository userRepositoryA;
@Inject
@Database(value = DatabaseType.KEY_VALUE, provider = "databaseB")
private UserRepository userRepositoryB;
//producers methods
@Produces
@Database(value = DatabaseType.KEY_VALUE, provider = "databaseA")
public BucketManager getManager() {
    BucketManager manager =//instance
    return manager;
}
@Produces
@Database(value = DatabaseType.KEY_VALUE, provider = "databaseB")
public BucketManager getManagerB() {
    BucketManager manager = //instance
    return manager;
}
```

Uma vez que a busca se dá, por padrão, pela busca da chave essa interface não suporte a geração de query, apenas a implementação dos métodos já existente.

```java
UserRepository userRepository = //instance
User user = new User("ada", "Ada Lovelace", 30);
List<User> users = Collections.singletonList(user);
userRepository.save(user);
userRepository.save(users);


Optional<User> userOptional = userRepository.findById("ada");
Iterable<User> usersFound = userRepository.findById(Collections.singletonList("ada"));
```

O recurso do dynamic query não suportado para o banco de dados do tipo Key-value.

