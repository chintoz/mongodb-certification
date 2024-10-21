# Unit 13 - MongoDB Indexes

## Lesson 1: Using MongoDB Indexes in Collections

Indexes are special structures which store a small portion of the data set in an easy-to-traverse form. They are ordered and prepare to search for data efficiently. Indexes are used to improve the performance of queries. They are used to find, sort, group, and project data. Indexes point to the document identity in the collection which allow to query or update data faster.

Indexes are used for:
1. Speed up queries
2. Reduce disk I/O
3. Reduce resources required
4. Support equality matches and range-based operations and return sorted results.

Indexed store data in an ordered form based on the index fields and values sort order which is provided when the index is created.

Without indexes, MongoDB must perform a collection scan, i.e., scan every document in a collection to select those documents that match the query statement. Even it sorts the results in memory. This is a slow operation and not scalable.
With indexes, MongoDb only fetches the documents identified by the index based on the query statement. It's faster and scalable. If the index contains all the information needed to satisfy the query, MongoDB can return the results using the index alone without looking inside the documents.

By default, there is one default index on the `_id` field. This index is unique and automatically created on the `_id` field. It's used to identify the document in the collection. Every query should use an index to improve performance. 

Indexes come with a write performance cost. When a document is inserted, updated, or deleted, the index must be updated. Therefore, it's important to create indexes that are used by queries. Write performance could be degraded if there are too many indexes or indexes that are not used. We should delete non used or redundant indexes.

The most common types of indexes are:

1. Single Field Indexes: Indexes on a single field. One index entry for each field value.
2. Compound Indexes: Indexes on multiple fields. One index entry for each combination of fields.
3. Multikey Indexes: Indexes on arrays. Each array entry has a corresponding index entry.

## Lesson 2: Creating a Single Field Index in MongoDB

Single field indexes are created on a single field. They support queries and sort on a single field. We can create a single field index using the `createIndex()` operation. The `createIndex()` operation creates an index on a field or fields. The operation has the following syntax:

```mongodb-json
db.collection.createIndex({ field: 1 })
```

1 means ascending order. -1 means descending order. The index is created in ascending order by default. The index is created in the background by default. The index is created in the foreground if the `background` option is set to false. The index is created unique if the `unique` option is set to true. The index is created sparse if the `sparse` option is set to true. The index is created with a name if the `name` option is set to a string.

The following example creates an index on the `birthdate` field in the `customers` collection:

```mongodb-json
db.customers.createIndex({ birthdate: 1 })
```

Mongo return index name. In this case the index name is `birthdate_1`. Another example, an index on email field in the customers collection which is unique:

```mongodb-json
db.customers.createIndex({ email: 1 }, { unique: true })
```

It prevents the insertion of documents with the same email value. If we try to insert a document with the same email value, Mongo will return an error about duplicate key. 

They way to retrieve all the indexes in a collection is using the `getIndexes()` method. The method returns an array of documents with the index information. The following example retrieves all the indexes in the customers collection:

```mongodb-json
db.customers.getIndexes()
```

The output from this command will show the indexes created for that collection:

```mongodb-json
[
  { "v": 2, "key": { "_id": 1 }, "name": "_id_" },
  { "v": 2, "key": { "birthdate": 1 }, "name": "birthdate_1" },
  { "v": 2, "unique": true, "key": { "email": 1 }, "name": "email_1", "background": true }
]
```

We use Atlas UI to review indexes created on a collection. On a collection we can show them in indexes section.

There is a way to check is the index is used by a query. We can use the `explain()` method to get information about the query execution. The `explain()` method returns a document with information about the query execution. The following example uses the `explain()` method to get information about the query execution. The query is about finding customer which birthdate is greater than 1st Agust 1995:

```mongodb-json
db.customers.explain().find({ birthdate: { $gt: new Date("1995-08-01") } })
```

The ouput of that operation will return query plan similar to this one:

```mongodb-json
{
  "queryPlanner": {
    "plannerVersion": 1,
    "namespace": "test.customers",
    "indexFilterSet": false,
    "parsedQuery": {
      "birthdate": {
        "$gt": ISODate("1995-08-01T00:00:00.000Z")
      }
    },
    "winningPlan": {
      "stage": "FETCH",
      "inputStage": {
        "stage": "IXSCAN",
        "keyPattern": {
          "birthdate": 1
        },
        "indexName": "birthdate_1",
        "isMultiKey": false,
        "multiKeyPaths": {
          "birthdate": []
        },
        "isUnique": false,
        "isSparse": false,
        "isPartial": false,
        "indexVersion": 2,
          "direction": "forward",
        "indexBounds": {
          "birthdate": [
            "(new Date(836457600000), new Date(253402214400000))"
          ]
        }
      }
    },
    "rejectedPlans": []
  },
  "serverInfo": {
    "host": "DESKTOP-1",
    "port": 27017,
    "version": "4.4.6",
    "gitVersion": "72e66213c2c3eab37d9358d5e78ad7f5c1d0d0d7"
  },
  "ok": 1
}
```

In case os several index could be used, the query planner will choose the best index to use. The `explain()` method is a good way to check if the index is used by a query. For example find by birthdate as the previous one, and sorting by email ascending:

```mongodb-json
db.customers.explain().find({ birthdate: { $gt: new Date("1995-08-01") }).sort({ email: 1 })
```

The plan generated is this one:

```mongodb-json
{
  "queryPlanner": {
    "plannerVersion": 1,
    "namespace": "test.customers",
    "indexFilterSet": false,
    "parsedQuery": {
      "birthdate": {
        "$gt": ISODate("1995-08-01T00:00:00.000Z")
      }
    },
    "winningPlan": {
      "stage": "SORT",
      "sortPattern": {
        "email": 1
      },
      "inputStage": {
        "stage": "FETCH",
        "inputStage": {
          "stage": "IXSCAN",
          "keyPattern": {
            "birthdate": 1
          },
          "indexName": "birthdate_1",
          "isMultiKey": false,
          "multiKeyPaths": {
            "birthdate": []
          },
          "isUnique": false,
          "isSparse": false,
          "isPartial": false,
          "indexVersion": 2,
          "direction": "forward",
          "indexBounds": {
            "birthdate": [
              "(new Date(836457600000), new Date(253402214400000))"
            ]
          }
        }
      }
    },
    "rejectedPlans": [
      {
        "stage": "FETCH",
        filter: {
          "birthdate": {
            "$gt": ISODate("1995-08-01T00:00:00.000Z")
          }
        },
        "inputStage": {
          "stage": "IXSCAN",
          "keyPattern": {
            "email": 1
          },
          "indexName": "email_1",
          "isMultiKey": false,
          "multiKeyPaths": {
            "email": []
          },
          "isUnique": true,
          "isSparse": false,
          "isPartial": false,
          "indexVersion": 2,
          "direction": "forward",
          "indexBounds": {
            "email": [
              "[MinKey, MaxKey]"
            ]
          }
        }
      }
    ]
  },
  "serverInfo": {
    "host": "DESKTOP-1",
    "port": 27017,
    "version": "4.4.6",
    "gitVersion": "72e66213c2c3eab37d9358d5e78ad7f5c1d0d0d7"
  },
  "ok": 1
}
```

In this case is better using `birthdate_1`index and ordering the outcome in memory than using `email_1` index.

When a query is executed and no index available for it, COLLECTION SCAN is used. It's a slow operation. It's important to create indexes for queries that are used frequently.

## Lesson 3: Creating a Multikey Index in MongoDB

TBC

## Lesson 4: Working with Compound Indexes in MongoDB

TBC

## Lesson 5: Deleting MongoDB Indexes

TBC