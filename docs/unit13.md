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

When we define an index on an array field, MongoDB creates a multikey index. Multikey indexes are used to index arrays. Each array entry has a corresponding index entry. Multikey indexes are used to query arrays. It can be a single field or compound index. The following examples based on customers collection with accounts field as an array are considered multikey indexes even when they are single or compound indexes:

```mongodb-json
db.customers.createIndex({ accounts: 1 })
```

```mongodb-json
db.customers.createIndex({ accounts: 1, email: 1 })
```

There is a limitation about one array field per index. The following example is not allowed if emails field is an array too:

```mongodb-json
db.customers.createIndex({ accounts: 1, emails: 1 })
```

Internally MongoDB creates an index entry for each element in the array. In the following example we are going to create an index on customers collection in the field accounts:

```mongodb-json
db.customers.createIndex({ accounts: 1 })
```

The outcome from that command will display index name, in this case it'll be `accounts_1`. 

## Lesson 4: Working with Compound Indexes in MongoDB

A compound index is an index on multiple fields. Can be a multikey index if it includes an array field with the limitation of one array field per index. This kind of indexes support queries that match on the prefix of index fields. The following example creates a compound index on the `birthdate` descendant, `active` and `name` fields in the `customers` collection:

```mongodb-json
db.customers.createIndex({ active: 1, birthdate: -1, name: 1 })
```

The following examples will show queries that can use the compound index created:

```mongodb-json
db.customers.find({active: true}).sort({birthdate: -1})
```

```mongodb-json
db.customers.find({ birthdate: { $lt: ISODate("1995-08-01") }, active: true })
```

And the following examples won't use the index because the are not starting from `active` field:

```mongodb-json
db.customers.find({ birthdate: { $lt: ISODate("1995-08-01") }})
```

```mongodb-json
db.customers.find({}).sort({birthdate: 1})
```

Compound indexes are ordered structures. The order of the fields in the index is important. The order of the fields in the index should match the order of the fields in the query. The way to define a compound index, and how to sort the fields should take into consideration this: Equality, Sort, Range fields. Appart from the order of the fields in term or sorting matter, sort order is important too. The sort order should match the sort order in the query.

### Equality, Sort, Range Fields

Equality test exact matches on single fields. Those fields should be placed first in the compound index. It reduces the query processing times, and it retrieves fewer documents. For instance:

```mongodb-json
findOne({active:true})
find({birthdate: ISODate("1995-08-01")})
```

Sort determines the order of results. Index sort eliminates the need for in-memory sorting. It should be placed after equality fields. Sort order is important if query results are sorted by more than 1 field and they mix sort orders (ascending and descending order). For instance:

```mongodb-json
sort({name: -1})
sort({birthdate: -1})
sort({name: -1, birthdate: 1})
```

Range field are queries based on a range of values. They should be placed after equality and sort fields to reduce in memory sort. It reduces the number of documents to scan. For instance:

```mongodb-json
find({birthdate: { $gt: ISODate("1995-08-01"), $lt: ISODate("1995-08-31") }})
```

Sometimes it could be considered adding additional fields for projection. It'll prevent doing document fetching because index could satisfy query results. If we add this kind of fields in order to improve projection performance, they should be placed after range fields.


## Lesson 5: Deleting MongoDB Indexes

It's recommended indexes to improve performance. However, it implies a write cost. Too many indexes in a collection can affect system performance. We should delete unused indexes to improve it. Deleting an index that's the only index supporting a query will affect the performance on the query. Then we have to make sure the index is not used before deleting it.

Recreating an index takes time an resources. It's better to disable an index than deleting it. We can disable an index using the `hideIndex()` method. The method has the following syntax:

```mongodb-json
db.collection.hideIndex("index_name")
```

It could receive as parameter index definition (the JSON used to define the index) or index name.

It hides the index but continues updating its keys. Try to group the indexes to reduce the number of redundant indexes. For example those queries:

```mongodb-json
find({username: "johndoe"})
find({username: "johndoe", active: true})
```
We can create two different indexes `username_1` and `username_1_active_1`. The first one could be redundant because the query could use the second one. We can hide the first one and keep the second one.

The operation to delete and index is the `dropIndex()` method. The method has the following syntax:

```mongodb-json
db.collection.dropIndex("index_name")
```

It could receive as parameter index definition (the JSON used to define the index) or index name.

We can also delete indexes from de Atlas UI. There is a button to Drop Index in the index list for a collection.

Apart from that command to delete one specific index, we can delete all indexes in a collection using the `dropIndexes()` method. The method has the following syntax:

```mongodb-json
db.collection.dropIndexes()
db.collection.dropIndexes('index_name')
db.collection.dropIndexes(['index_name1', 'index_name2'])
```

It's going to delete all indexes except _id index. This command would support and index as parameter or an array of indexes to be deleted.