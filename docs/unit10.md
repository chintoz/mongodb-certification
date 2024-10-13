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

TBC