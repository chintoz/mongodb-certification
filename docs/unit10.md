# MongoDB CRUD Operations in Java

## Lesson 1: Working with MongoDB Documents in Java

BSON is the data format that MongoDB uses to organize and store data. BSON is optimized for storage, retrieval and transmission accross the wire. It's more secure than plain text JSON which is more sensible for injection attack. BSON supports more data types than JSON. It supports more data types than JSON. It supports data types like Date, Binary Data, Regular Expression, etc.

MongoDB Java driver offers a way to work with BSON data. It's done via `Document` class. `Document` class provides flexible and concise data representation. It's a key-value pair data structure. It's similar to a Map in Java

If don't provide and `_id` field, MongoDB will generate it automatically. The `_id` field is unique in the collection. It's a good practice to provide it. It's going to be easier to identify the document.

Here an example about generating a `Document` object in Java:
    
```java
Document doc = new Document("_id", new ObjectId())
    .append("business_id", "123456")
    .append("certification_number", 123123)
    .append("business_name", "Superior Bagel")
    .append("data", Date.from(LocalDate.of(2020, 1, 1).atStartOfDay(ZoneId.systemDefault()).toInstant()))
    .append("result", "No violation issued")
    .append("address", new Document("building", "1007")
        .append("coord", Arrays.asList(-73.856077, 40.848447))
        .append("street", "Morris Park Ave")
        .append("zipcode", "10462"));
```

Mongo recommends that you use embedded documents when you model your document schema. It's a good practice to use embedded documents when you have a one-to-one relationship between the documents. It's a good practice to use embedded documents when you have a one-to-many relationship between the documents. It's a good practice to use embedded documents when you have a many-to-many relationship between the documents.



## Lesson 2: Inserting a Document in Java Applications

The `insertOne()` method is used to insert a document in a collection. The basic structure is:

```java
public static void main(String[] args) {
    
    String mongoConnectionString = "mongodb://localhost:27017";
    try (MongoClient mongoClient = MongoClients.create(mongoConnectionString)) {
        List<Document> databases = mongoClient.listDatabases().into(new ArrayList<>());    
        MongoDatabase database = mongoClient.getDatabase("sample_training");
        MongoCollection<Document> collection = database.getCollection("inspections");

        Document inspection = new Document("_id", new ObjectId())
            .append("business_id", "123456")
            .append("certification_number", 123123)
            .append("business_name", "Superior Bagel")
            .append("data", Date.from(LocalDate.of(2020, 1, 1).atStartOfDay(ZoneId.systemDefault()).toInstant()))
            .append("result", "No violation issued")
            .append("address", new Document("building", "1007")
                .append("coord", Arrays.asList(-73.856077, 40.848447))
                .append("street", "Morris Park Ave")
                .append("zipcode", "10462"));

        InsertOneResult result = collection.insertOne(inspection);
        BsonObjectId id = result.getInsertedId();
        System.out.println("Inserted document with id: " + id); 
    }
}
```

In this case, it's going to be shown the id value generated for the document inserted.

The `insertMany()` method is used to insert multiple documents in a collection. The basic structure is:

```java
public static void main(String[] args) {
    
    String mongoConnectionString = "mongodb://localhost:27017";
    try (MongoClient mongoClient = MongoClients.create(mongoConnectionString)) {
        List<Document> databases = mongoClient.listDatabases().into(new ArrayList<>());    
        MongoDatabase database = mongoClient.getDatabase("bank");
        MongoCollection<Document> collection = database.getCollection("accounts");

        Document doc1 = new Document()
                .append("account_holder", "Jane Doe")
                .append("account_id", "MDB1001")
                .append("account_type", "checking")
                .append("balance", 5000.00);

        Document doc2 = new Document()
                .append("account_holder", "John Doe")
                .append("account_id", "MDB1002")
                .append("account_type", "checking")
                .append("balance", 10000.00);
        
        List<Document> documents = Arrays.asList(doc1, doc2);
        InsertManyResult result =  collection.insertMany(documents);
        result.getInsertedIds().forEach((k, v) -> System.out.println("Inserted document with id: " + v));

    }
}
```

## Lesson 3: Querying a MongoDB Collection in Java Applications

There are couple of method to find documents in a collection. The `find()` method is used to find all documents in a collection. The basic structure is:

```java
public static void main(String[] args) {
    
    String mongoConnectionString = "mongodb://localhost:27017";
    try (MongoClient mongoClient = MongoClients.create(mongoConnectionString)) {
        List<Document> databases = mongoClient.listDatabases().into(new ArrayList<>());    
        MongoDatabase database = mongoClient.getDatabase("bank");
        MongoCollection<Document> collection = database.getCollection("accounts");

        Bson filters = Filters.and(Filters.gte("balance", 1000), Filters.eq("account_type", "checking"));
        FindIterable<Document> documents = collection.find(filters);
        documents.forEach(doc -> System.out.println(doc.toJson()));
    }
}
```

We can use an iterator to iterate across the results. In the following example is used a try with resources to ensure the resources are closed after the iteration:

```java
public static void main(String[] args) {
    
    String mongoConnectionString = "mongodb://localhost:27017";
    try (MongoClient mongoClient = MongoClients.create(mongoConnectionString)) {
        List<Document> databases = mongoClient.listDatabases().into(new ArrayList<>());    
        MongoDatabase database = mongoClient.getDatabase("bank");
        MongoCollection<Document> collection = database.getCollection("accounts");

        Bson filters = Filters.and(Filters.gte("balance", 1000), Filters.eq("account_type", "checking"));
        try (MongoCursor<Document> cursor = collection.find(filters).iterator()) {
            while (cursor.hasNext()) {
                Document document = cursor.next();
                System.out.println(document.toJson());
            }
        }
    }
}
```

In certain cases we could want to return only a single document. The `find()` method returns a `FindIterable` object. The `first()` method is used to return the first document in the collection. The basic structure is:

```java
public static void main(String[] args) {
    
    String mongoConnectionString = "mongodb://localhost:27017";
    try (MongoClient mongoClient = MongoClients.create(mongoConnectionString)) {
        List<Document> databases = mongoClient.listDatabases().into(new ArrayList<>());    
        MongoDatabase database = mongoClient.getDatabase("bank");
        MongoCollection<Document> collection = database.getCollection("accounts");

        Bson filters = Filters.and(Filters.gte("balance", 1000), Filters.eq("account_type", "checking"));
        Document document = collection.find(filters).first();
        System.out.println(document.toJson());
    }
}
```

All query should provide filters to prevent retrieving all the document from a collection. It's going to improve the performance of the query. It's going to reduce the amount of data transferred between the database and the application. It's going to reduce the amount of memory used by the application.

## Lesson 4: Updating Documents in Java Applications

For updating documents we have a couple of method for this purpose. The `updateOne()` method is used to update a single document in a collection. The `updateMany()` method is used to update multiple documents in a collection. 

Regarding the operation to be executed we have two possibilities, update the value of an existing field or add a new field to the document.

`updateOne()` method is used to update a single document in a collection when it matches a specific criteria.This method accepts a query filter, and an update document. The basic structure is:

```java
collection.updateOne(filter, update);
```

In the following example we are going to update the balance of an account. The account_id is the filter and the new balance is the update and account_status is going to be updated to active:

```java
public static void main(String[] args) {

    String mongoConnectionString = "mongodb://localhost:27017";
    try (MongoClient mongoClient = MongoClients.create(mongoConnectionString)) {
        List<Document> databases = mongoClient.listDatabases().into(new ArrayList<>());
        MongoDatabase database = mongoClient.getDatabase("bank");
        MongoCollection<Document> collection = database.getCollection("accounts");
        
        Bson filter = Filters.eq("account_id", "MDB1001");
        Bson update = Updates.combine(Updates.inc("balance", 6000.00), Updates.set("account_status", "active"));
        
        UpdateResult updateResult = collection.updateOne(filter, update);
        

    }
}
```

Regarding arrays there are several operations: Updates.push(), Updates.pull(), Updates.addToSet(), Updates.pop(), Updates.pullAll(), Updates.pushEach(). The `push()` method is used to add an element to an array field. The basic structure is:

```java
collection.updateOne(filter, Updates.push("diet", "vegan"));
```

To access to a specific position in the array we should use the `$` operator. The `$` operator is used to update the first element that matches the query. The basic structure is:

```java
collection.updateOne(filter, Updates.set("diet.$", "vegan"));
```

Regarding update many operation, there is a method called `updateMany()` which is used to update multiple documents in a collection. The basic structure is:

```java
collection.updateMany(filter, update);
```

In the following example we are going to set a minimum balance for all accounts of type savings:

```java
public static void main(String[] args) {

    String mongoConnectionString = "mongodb://localhost:27017";
    try (MongoClient mongoClient = MongoClients.create(mongoConnectionString)) {
        List<Document> databases = mongoClient.listDatabases().into(new ArrayList<>());
        MongoDatabase database = mongoClient.getDatabase("bank");
        MongoCollection<Document> collection = database.getCollection("accounts");
        
        Bson filter = Filters.eq("account_type", "savings");
        Bson update = Updates.combine(Updates.set("balance", 1000.00));
        
        UpdateResult updateResult = collection.updateMany(filter, update);
        

    }
}
``` 

## Lesson 5: Deleting Documents in Java Applications

Regarding delete operations, there are a couple of methods to be used in order to delete documents. The `deleteOne()` method is used to delete a single document in a collection. The `deleteMany()` method is used to delete multiple documents in a collection.

`deleteOne()` method is used to delete a single document in a collection when it matches a specific criteria. This method accepts a query filter. The basic structure is:

```java
collection.deleteOne(filter);
```

It returns an object of type `DeleteResult`. The `getDeletedCount()` method is used to get the number of documents deleted. The basic structure is:

```java
DeleteResult deleteResult = collection.deleteOne(filter);
System.out.println("Deleted documents: " + deleteResult.getDeletedCount());
```

Here a full example relate with accounts collection, and delete the document for a account holder called John Doe:

```java
public static void main(String[] args) {

    String mongoConnectionString = "mongodb://localhost:27017";
    try (MongoClient mongoClient = MongoClients.create(mongoConnectionString)) {
        List<Document> databases = mongoClient.listDatabases().into(new ArrayList<>());
        MongoDatabase database = mongoClient.getDatabase("bank");
        MongoCollection<Document> collection = database.getCollection("accounts");
        
        Bson filter = Filters.eq("account_holder", "John Doe");
        DeleteResult deleteResult = collection.deleteOne(filter);
        System.out.println("Deleted documents: " + deleteResult.getDeletedCount());
    }
}
```

If the `deleteOne()` operation is launched without filter, it's going to delete the first document in the collection. It's a good practice to provide a filter to prevent deleting all the documents in a collection.

`deleteMany()` method is used to delete multiple documents in a collection when it matches a specific criteria. This method accepts a query filter. The basic structure is:

```java
collection.deleteMany(filter);
```

It returns an object of type `DeleteResult`. The `getDeletedCount()` method is used to get the number of documents deleted. The basic structure is:

```java
DeleteResult deleteResult = collection.deleteMany(filter);
System.out.println("Deleted documents: " + deleteResult.getDeletedCount());
```

Here a concrete example deleting all the accounts with the status dormant:
    
```java 
public static void main(String[] args) {

    String mongoConnectionString = "mongodb://localhost:27017";
    try (MongoClient mongoClient = MongoClients.create(mongoConnectionString)) {
        List<Document> databases = mongoClient.listDatabases().into(new ArrayList<>());
        MongoDatabase database = mongoClient.getDatabase("bank");
        MongoCollection<Document> collection = database.getCollection("accounts");
        
        Bson filter = Filters.eq("account_status", "dormant");
        DeleteResult deleteResult = collection.deleteMany(filter);
        System.out.println("Deleted documents: " + deleteResult.getDeletedCount());
    }
}
```

`deleteMany()` without any filter is going to delete all the documents in the collection. It's a good practice to provide a filter to prevent deleting all the documents in a collection.

## Lesson 6: Creating MongoDB Transactions in Java Applications

A multi-document transaction is an operation that requires atomicity of reads and/or writes to multiple documents. A transaction is a sequence of database operations that represent a single unit of work. A transaction is an all-or-nothing operation. It means that if one operation fails, the entire transaction fails. It's going to roll back all the operations. It's going to ensure the data integrity.

On commited all the operations performed under the protection of the transaction are saved. If the transaction is cancelled or doesn't complete, all write operations performed are discarded. It's going to roll back all the operations.

Operations within a transaction success of fail together. This property is called atomicity. It's going to ensure the data integrity. It's going to ensure the data consistency.

ACID transactions are defined by: Atomicity, Consistency, Isolation, Durability. Atomicity means that all the operations in the transaction are performed or none of them are. Consistency means that the data is in a consistent state before and after the transaction. Isolation means that the transaction is isolated from other transactions. Durability means that the data is saved and won't be lost.

Transaction steps:

1. Start a client session.
2. Define transaction options (optional).
3. Define the sequence of operations to be performed inside the transaction.
4. Start the transaction by using ClientSession's `withTransaction()` method.
5. Release the resources used by the transaction.

Remember:
1. There's 60 seconds timeout for a transaction.
2. Close any resources used by the transaction.

In the following example we are going to transfer money between two accounts. The transaction is going to be performed in the bank database:

```java
public static void main(String[] args) {

    String mongoConnectionString = "mongodb://localhost:27017";
    final MongoClient mongoClient = MongoClients.create(mongoConnectionString);
    final ClientSession clientSession = mongoClient.startSession();
    
    TransactionBody txnBody = new TransactionBody<String>() {
        @Override
        public void execute() {
            MongoCollection<Document> bankingCollection = clientSession.getDatabase("bank").getCollection("accounts");
            
            Bson fromAccount = Filters.eq("account_id", "MDB1001");
            Bson withdrawal = Updates.inc("balance", -1000.00);
            
            Bson toAccount = Filters.eq("account_id", "MDB1002");
            Bson deposit = Updates.inc("balance", 1000.00);

            System.out.println("This is from account " + fromAccount.toBsonDocument().toJson() + " withdrawal " + withdrawal.toBsonDocument().toJson());
            System.out.println("This is to account " + toAccount.toBsonDocument().toJson() + " deposit " + deposit.toBsonDocument().toJson());
            
            bankingCollection.updateOne(clientSession, fromAccount, withdrawal);
            bankingCollection.updateOne(clientSession, toAccount, deposit);
            
            return "Transferred funds";
            
        }
    };
    
    try {
        clientSession.withTransaction(txnBody);
    } catch (MongoException e) {
        System.out.println("Error in transaction: " + e.getMessage());
    } finally {
        clientSession.close();
    }
}
```

In case of the process encounter an error, transaction is going to be cancelled. If everything goes well transaction is going to be commited.

