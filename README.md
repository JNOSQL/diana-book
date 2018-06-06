# Eclipse JNoSQL

## JNOSQL

The JNoSQL is a framework whose has the goal to help Java developers to create Java EE applications with NoSQL, whereby they can make scalable application beyond enjoy the polyglot persistence. It has two layers/API.

* **Communication API**: An API just to communicate with the database, exactly what JDBC does to SQL. This API has four specializations, one for each kind of database.
* **Mapping API**: An API to do integration and do the best integration with the Java developer. It's annotation driven and integrated with other technologies like Bean Validation, etc. 

The basic building blocks hereby are:

* A simple API to support Column NoSQL Database
* A simple API to support Key-value NoSQL Database
* A simple API to support Graph NoSQL Database
* A simple API to support Document Database
* Convention over configuration
* Support for asynchronous queries
* Support for asynchronous write operations
* An easy API to implement, so that NoSQL vendors can comply with it and test by themselves.

The API's focus is on simplicity and ease of use. Developers should only have to know a minimal set of artifacts to work with the solution. The API is built on latest Java 8 features and therefore fit perfectly with the functional features of Java 8.

## Diana

The Diana Project has as the goal just be the flat layer, in other words, just the communication layer to NoSQL database. This project works as a database driver. Diana has four APIs, one for each database type, and its TCK respective. The test compatibility kit affirms if a driver implements an API correctively. So an X database of key-value implements and run all tests correctively that means this X database has support to key-value Diana API.

## Artemis

Artemis is an integration layer, in other words, it has the goal to communicate with the communication layer, Diana, and it does integrations with other technologies such as Bean Validation. The Artemis engine has CDI. So it formula is really simple:

### Diana plus CDI equals to Artemis

Look like Diana; it has a different package to each NoSQL database. Using CDI as heart, Artemis is highly customizable also observe events on the persistence flow. Artemis has a nice feature such as:

* Annotation based
* Highly customizable \\(reflection component, how to save the cache, persistence workflow, etc.\\)
* Listen to an event to each kind of persistence workflow \\(it has differents kind of events to each database\\).
* Use interceptors

Using CDI events to add a new feature on Artemis is easy and you also can it on transparency way, you don't need to change the repositories. E.g., Using events can add bean validation on the workflow, without the DAO layer know.

