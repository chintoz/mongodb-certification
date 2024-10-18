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

## Lesson 3: Using MongoDB Aggregation Stages with Java: $sort and $project

In the sane way as the previous lesson, we are going to use Aggregates builder to define the stages with `$sort` and `$project` operations.

`$sort` sorts all input documents. Value 1 means ascending order. Value -1 means descending order.

`$project` determines output shape of documents. It can be used to include, exclude, rename, or add fields. It should be the last stage to format the output in the pipeline.

In the following example we are going to find all checking accounts with balance greater than 1500, sort the result in descending order. It's going to return only the original balance, euro balance, account type and account id fields. It's going to be assumed balance is in dolar and euro balance is going to be calculated a ratio 1 euro is 1.2 dollars.

```java
public class Sample {
    public static void main(String[] args) {
        String connectionString = "mongodb://localhost:27017";
        try (MongoClient mongoClient = MongoClients.create(connectionString)) {
            MongoDatabase database = mongoClient.getDatabase("bank");
            MongoCollection<Document> accounts = database.getCollection("accounts");
            matchSortAndProjectStages(accounts);
        }
    }

    private static void matchSortAndProjectStages(MongoCollection<Document> accounts) {
        Bson matchStage = Aggregates.match(Filters.and(
                Filters.eq("account_type", "checking"),
                Filters.gt("balance", 1500)
        ));
        Bson sortStage = Aggregates.sort(Sorts.orderBy(Sorts.descending("balance")));
        Bson projectStage = Aggregates.project(
                Projections.fields(
                        Projections.include("balance", "account_type", "account_id"),
                        Projections.computed("euro_balance", new Document("$divide", Arrays.asList("$balance", 1.2))),
                        Projections.excludeId()
                )
        );
        System.out.println("Displaying aggregation results:");
        accounts.aggregate(Arrays.asList(matchStage, sortStage, projectStage)).forEach(doc -> System.out.println(doc.toJson()));
    }
}
```