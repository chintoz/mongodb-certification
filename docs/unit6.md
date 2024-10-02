# Unit 6 - Connecting to MongoDB in Java

## Lesson 1 - Using MongoDB Java Client libraries.

We will need a library to connect with the driver. There are two versions of the driver Synchronous and asynchronous. This unit will be focused on synchronous one.

Drivers

* Simplify connecting and interacting with the mongodb cluster
* Establish secure connections to a MongoDB cluster 
* Execute database operations on behalf of client applications
* Specify connection options.

Official driver for most adhere languages (driver follows each programming language best practices). Driver uses the full functionality of Mongo DB deployment. Driver makes upgrading easier.


## Lesson 2 - Connecting to an Atlas Cluster in Java applications

Steps to be followed:

1. Configure Java application via Maven to use the MongoDB Java driver (synch one)
2. Provide a valid connection string to our database.

MongoClientSetting allows to control the setting for the client instance to connect to the database. With this code snippet it’s possible to connect to a database in the cluster and check the collections in it:


```java
String connectionString = "mongodb+srv://xxxxx:xxxxxxx@myatlasclusteredu.xxxxxx.mongodb.net/?retryWrites=true&w=majority&appName=myAtlasClusterEDU";

ServerApi serverApi = ServerApi.builder().version(ServerApiVersion.V1).build();

MongoClientSettings settings = MongoClientSettings.builder()
    .applyConnectionString(new ConnectionString(connectionString))
    .serverApi(serverApi)
    .build();

// Create a new client and connect to the server
try (MongoClient mongoClient = MongoClients.create(settings)) {
    try {
        // Send a ping to confirm a successful connection
        MongoDatabase database = mongoClient.getDatabase("sample_analytics");
        database.runCommand(new Document("ping", 1));
        System.out.println("Pinged your deployment. You successfully connected to MongoDB!");
        System.out.println("Collections: " +
        StreamSupport.stream(database.listCollectionNames().spliterator(), false)
        .collect(Collectors.joining(",")));
    } catch (MongoException e) {
        e.printStackTrace();
    }
}
```

We have to provide API version. There is a builder helper for it.

An application should use a single MongoClient instance for all database requests. It manages connection with the database. Instantiating a MongoClient instance is a resource intensive process. The idea could be implement a singleton pattern for it.

It looks like “admin” is always created. That’s the reason why the examples are using admin database to connect to it.

According to documentation which two build systems could be used to add MongoDB Java driver dependency in java? Maven and Gradle


## Lesson 3 - Troubleshooting a MongoDB connection in Java applications

Connection issues:

1. IP address is not whitelisted. It’s configured in Network access and we can add current IP.
2. Connection String format. Connection string must be a valid URI. Any of those characters: !, *, ‘, (, ), ;,:, @, &, =, +, $, “,”, /, ?, %, #, [,] must be percent encoded. Mainly in passwords. Incorrect username or password will result in authentication error. Two main reasons:
Unencoded special characters
User not defined in the default admin database. If the user was defined in another database, we have to provide authSource parameter for it.
3. Firewall misconfiguration. It should allow to connect using the default port 27017.
4. Errors due to too many open connections. Atlas sets limits for concurrent incoming connections. Limit is independent on cluster tier. In case of errors due to connection limit we could do
   1. Restarting the application
   2. Closing any open connections to your database that are not currently in use
   3. Scaling your cluster to a higher tier to support more connections
   4. Limiting the number of connections in the connection pool by using the maxPoolSize connection string option.
3. We have to ensure our Java application calls MongoClient.close() method to release resources. 
