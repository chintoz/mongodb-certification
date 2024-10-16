# MongoDB Aggregation with Java

## Lesson 1: Building a MongoDB Aggregation Pipeline in Java Applications

If the query to be executed can be done using `find()` method, it's recommended to use it instead of aggregation. Aggregation is more complex and slower than `find()` method. However, aggregation is useful when we need to perform complex operations like grouping, sorting, filtering, and transforming data. 

Aggregation pipelines are composed by stages and expressions. Aggregation pipelines are used for finding, sorting, grouping, and projecting data. This approach based on stages simplifies debug and maintain individuals stages. Even it's easier for MongoDB to rewrite and optimize the query.

Stages can use expression operator, literal values, and variables. Expression operators are similar to functions which takes field attributes and return a value. Literal values are fixed values like strings, numbers, and booleans. Variables are used to store values and reuse them in the pipeline.

## Lesson 2: Using MongoDB Aggregation Stages with Java: $match and $group

We are going to start with an example using a match and group aggregations. We are going to be based con accounts database, and we are going to find an account belonging to "Puja Barbier", given an account number and we are going to return the average balance and the total balance for the savings and checking accounts.

```java
public class Sample {
    public static void main(String[] args) {
        String connectionString = "mongodb://localhost:27017";
        try (MongoClient mongoClient = MongoClients.create(connectionString)) {
            MongoDatabase database = mongoClient.getDatabase("bank");
            MongoCollection<Document> accounts = database.getCollection("accounts");
            matchAndGroupStages(accounts);
        }
    }

    private static void matchAndGroupStages(MongoCollection<Document> accounts) {
        Bson matchStage = Aggregates.match(Filters.eq("account_id", "MDB123123123123"));
        Bson groupStage = Aggregates.group("$account_type",
                Accumulators.avg("average_balance", "$balance"),
                Accumulators.sum("total_balance", "$balance")
        );
        System.out.println("Displaying aggregation results:");
        accounts.aggregate(Arrays.asList(matchStage, groupStage)).forEach(doc -> System.out.println(doc.toJson()));
    }

    /**
     * Used as an initial example to show how to do it for only one stage. After that, it was created next method to do it with two stages.
     */
    @Deprecated
    private static void matchStage(MongoCollection<Document> accounts) {
        Bson matchStage = Aggregates.match(Filters.eq("account_id", "MDB123123123123"));
        System.out.println("Displaying aggregation results:");
        accounts.aggregate(Arrays.asList(matchStage)).forEach(doc -> System.out.println(doc.toJson()));
    }
    
    
}
```