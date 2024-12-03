# Spring Data and MongoDB

## Learning Byte

Spring is a framework for building complex Java applications such as web apps, microservices, and batch jobs. A way to use data access technology, relational and non-relational database, map-reduce frameworks, and cloud-based data services from Java.

Spring Data MongoDB includes:

* A POJO centric model
* Spring configuration support
* A MongoTemplate helper class
* A MongoRepository interface
* Feature-rich object mapping

`@Document` annotation is used to map a domain object to a MongoDB collection. The `@Id` annotation is used to mark a field as the primary key. If we dont specify `@Id`, the field _id will be used as the primary key.

Once we have the "entity", we can create a repository interface that extends MongoRepository. This interface provides CRUD operations and other common operations. For MongoDB, our interface should inherit from `MongoRepository<Entity, Id>`. This base interface offers CRUD operations and other common operations.

We can define our own queries annotating the method with `@Query`. We can also use the Query DSL to create queries.

Once we start spring application, we can enable mongo repositories by annotating the main class with `@EnableMongoRepositories`.


