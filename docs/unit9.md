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

TBC