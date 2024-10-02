# Unit 7 - MongoDB CRUD Operations: Insert and Find Documents

## Lesson 1 - Inserting documents in a MongoDB collection

There are two methods to insert documents:

* `insertOne` -> `db.<collection>.insertOne({})`. It will provide the id of the document created.
* `insertMany` -> `db.<collection>.insertMany([{}, {}, {} ..])`. It will return an array of ids.


If the collection doesn’t exist, mongo will create it. Be careful to avoid creating unnecessary collections.

If it’s not provided _id field, the value will be generated automatically by mongoldb.

## Lesson 2: Finding Documents in a MongoDB Collection

The method to find

* `find` -> `db.<collection>.find()`. In the shell, the results are paginated, if we want to show more results we should launch the command  > it to iterate to the next page. In the end, it’s opening a cursor for the query.

For a query for an specific value there are two ways:

* `{field : { $eq: <value>}}`
* `{field: <value>}`

There is an interesting operator the `$in` one. `$in` operator allows us to select all documents that have a field value equal to any of the values specified in the array. This is the structure of the query:

```mongodb-json
db.<collection>.find({field: {$in: [<value1>, <value2> …]}})
```

Example finding by ObjectID

```mongodb-json
db.zips.find({ _id: ObjectId("5c8eccc1caa187d17ca6ed16") })
```

## Lesson 3: Finding Documents by Using Comparison Operators

In this section is going to describe these operators

* `$gt` (greater than) - Returns documents where the field contains a value greater than the specified value.
* `$lt` (less than) - Returns documents where the field contains a value less than the specified value.
* `$lte` (less than or equal to) - Returns all documents less than or equal to a given number.
* `$gte` (greater than or equal to) - Returns all documents greater than or equal to a given number.


$ character differentiate field from operators. The basic structure to do queries is

```mongodb-json
{field: {operator: <value>}}
```

In case we want to access to a nested field the structure to be used is:

```mongodb-json
{field.nestedField: {operator: 	<value>}}
```

## Lesson 4: Querying on Array Elements in MongoDB


To query arrays in documents, we will need to use this operator:

* `$elemMatch`

The idea if finding document which contains the elements in the array. In this case we have to use this operator and a comparator, for example:

```mongodb-json
db.accounts.find({products: {$elementMatch : {$eq: “InventmentStock”}}})
```

In this case it’ll find in the array this element.
This operator offers the possibility to check several query statements like this:

```mongodb-json
{field: {elemMatch: {<query1>, <query2>, … }}}
```

In this view is shown a query with `findOne() find operator.

We can find elements in the array without the elementMatch operator it means we are waiting one item with this value such as:

```mongodb-json
db.accounts.find({ products: "CurrencyService" }) - This query will return all documents with the field products and the scalar value “CurrencyService” or those documents with an array, and “CurrencyService” is one of the elements of the array.
```

It’s returning

```mongodb-json
{
_id: ObjectId('5ca4bbc7a2dd94ee581623bd'),
account_id: 918103,
limit: 10000,
products: [ 'CurrencyService', 'Brokerage', 'Commodity', 'InvestmentStock' ]
}
```

This is an example of a query over several fields:

```mongodb-json
db.transactions.find(({
transactions: {
$elemMatch: {amount: {$lte: 4500}, transaction_code: {$eq: "sell"}}}
})
```

## Lesson 5: Finding Documents by Using Logical Operators

Logical operators

* `$and` - logical and of an array of expressions

```mongodb-json
db.collection.find({ $and: [{expression}, {expression} ….]})
```

Furthermore, it has an explicit way:

```mongodb-json
db.collection.find({<expression>, <expression> …})
```

* `$or` - logical or of an array of expressions

```mongodb-json
db.collection.find({ $or: [{expression}, {expression} ….]})
```

We can combine `$and` and `$or` operators. When it’s combined $and and $or we cannot use explicit syntax. The reason is because we cannot define the same field several times in a JSON object, it means, we cannot configure a JSON with several $or fields. In this case we have to use array notation for $and expression.

Example combining operators:

```mongodb-json
db.routes.find({
$and: [
{ $or: [{ dst_airport: "SEA" }, { src_airport: "SEA" }] },
{ $or: [{ "airline.name": "American Airlines" }, { airplane: 320 }] },
]
})
```
