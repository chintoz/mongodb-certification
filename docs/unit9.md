# Unit 9 - MongoDB CRUD Operations: Modifying Query Results

## Lesson 1: Sorting and Limiting Query Results in MongoDB

A cursor is a pointer to the result set of a query. For instance `find()` method returns a cursor. The cursor is an iterable object that allows to iterate over the results. The cursor is not the result itself. The result is the documents that match the query.

Cursor methods which are chained to queries, they allow us to perform actions on the result set. The most common methods are `sort()` and `limit()`.

First operation to be described is `sort()`. It allows to sort the result set based on the field and the order. The basic structure is:

```mongodb-json
db.companies.find({"category_code": "music"}).sort({"name": 1})
```
Value 1 means ascending order. Value -1 means descending order.

We can sort by different fields. Imagine an scenario which the query is projecting a subset of fields:

```mongodb-json
db.companies.find({"category_code": "music"},{"name":1}).sort({"name": 1})
```

In this example, capitalization is going to be considered. It means capital letters are going to be sorted before lowercase letters. If we want to do it ignoring the case, we have to use collation.

```mongodb-json
db.companies.find({"category_code": "music"},{"name":1}).sort({"name": 1}).collation({locale: "en", strength: 2})
```

The second operation is `limit()`. It allows to limit the number of documents returned by the query. The basic structure is:

```mongodb-json
db.companies.find({"category_code": "music"}).sort({"number_of_employees": -1}).limit(3)
```

In the previous query, it was returned the 3 companies with the highest number of employees.

Limiting the number of documents returned by the query is a good practice. It reduces the amount of data transferred between the database and the application. It also reduces the amount of memory used by the application. In summary, it improves the performance of the application.

## Lesson 2: Returning Specific Data from a Query in MongoDB

By default, mongodb return all fields in the documents. It’s possible to project a subset of fields. Projection basic structure is:

```mongodb-json
db.inspections.findOne({"sector": "Restaurant - 818"}, {"business_name": 1, "result": 1})
```

This will improve the performance of the query. It’s going to reduce the amount of data transferred between the database and the application. It’s going to reduce the amount of memory used by the application.

Even when we project some fields, by default the _id field is going to be returned. If we don’t want to return it, we have to explicitly exclude it.

```mongodb-json
db.inspections.findOne({"sector": "Restaurant - 818"}, {"business_name": 1, "result": 1, "_id": 0})
```

Inclusion and exclusion statements can’t be mixed in the same projection. If we want to include and exclude fields, we have to do it in two different projections. _id field is the only field that can be excluded. We can choose include or exclude fields, but not both.

Now we are going to show an example of exclusion:

```mongodb-json 
db.inspections.findOne({"result": {$in: ["Pass", "Warning"]}}, {"date": 0, "address.zip": 0})
```

At the time of defining fields, if the field is nested, we have to use the dot notation. In the previous example, the field address is nested in the document. In the previous example date field won't need quotes, but address.zip will need it.

## Lesson 3: Counting Documents in a MongoDB Collection

In this lesson, we are going to show how to count documents in a collection. The basic structure is:

```mongodb-json
db.collectionName.countDocuments(<query>, {<options>})
```

The first parameter is the query. It’s optional. If we don’t provide it, it’s going to count all documents in the collection. The second parameter is the options. It’s optional. It allows to specify the limit of documents to be counted. The options document is rarely used.

Here an example counting  documents in a collection:

```mongodb-json
db.trips.countDocuments()
```

In the following example, it's going to count the documents which trip duration is bigger than 120 and user type is subscriber:

```mongodb-json
db.trips.countDocuments({"tripduration": {$gt: 120}, "usertype": "Subscriber"})
```

Here an example in a lab. In the following example is going to query the amount of items in the collection which name is laptop and price is less than 600:

```mongodb-json
db.sales.countDocuments({ items: { $elemMatch: { name: "laptop", price: { $lt: 600 } } } } )
```