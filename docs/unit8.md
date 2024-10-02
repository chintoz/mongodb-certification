# Unit 8 - MongoDB CRUD Operations: Replace and Delete Documents

## Lesson 1: Replacing a document in MongoDB

We are going to use this method to replace a document

```mongodb-json
db.collection.replaceOne(filter, replacement, options)
```

When this operation is launched, it’s returned a summary with the action which contains matchedCount and modifiedCount values. In this case the value cannot be bigger than 1.

This operation will override whole document, not some fields. The document is going to be completely overridden.

# Lesson 2: Updating MongoDB Documents by Using updateOne()

It’s going to be based on the operation `updateOne()` and the two existing operators:

* `$set` - This operator add new fields and values to a document or replaces the value of a field with a specified value.
* `$push` - Appends a value to an array or if absent, it add the array field with the value as its element.

And the option upsert

The structure of and updateOne command is

```mongodb-json
db.colletion.updateOne(<filter>, <update>, {options})
```

Filter contains the query for the document. Update contains the operations to do changes on the document. Options is optional.

In the same way as the previous operation (replaceOne) it returns a summary with the action which contains matchedCount and modifiedCount values.

In case of the filter is not returning any document, we could want to create it. In this case we could use upsert operation for it. Upsert operation inserts a document with provided information if matching documents don’t exist. In case of the document exist, the update operation is executed. In case of the document is created, it uses information from filter and information from update to populate it.

Example with upsert option:

```mongodb-json
db.collection.updateOne(filter, update, {upsert: true})
```

Simple push operation is considered for adding one value to an array. If I want to add more values to an array (starting from another array) we have to use $each operator like this:

```mongodb-json
db.birds.updateOne({common_name: "Northern Cardinal"},{$push: {diet: {$each: ["diet2", “diet3"]}}})
```

Example upsert insert fields from query and from update section like this command executed:

```mongodb-json
db.birds.updateOne({common_name: "Northern Cardinal2"},{$push: {diet: {$each: ["diet2", "diet3"]}}},{upsert: true})
```

It will create this document:

```mongodb-json
{
"_id" : ObjectId("66b3e7f9cd032680b4bfdb43"),
"common_name" : "Northern Cardinal2",
"diet" : [ "diet2", "diet3" ]
}
```

## Lesson 3: Updating MongoDB Documents by Using findAndModify()

The operation `findAndModify()` returns the document that has just been updated. It’s the same as call `updateOne()` operation to update a document, and after that execute `findOne()` to retrieve the updated document. The drawbacks of this approach is about doing two operations with the server (performance), and the risk of another update in between update and find which will return us a different version of the document.

This is an example of the usage of it:

```mongodb-json
db.podcasts.findAndModify({
    query: {_id: ObjectId(123123)},
    update: {$inc: {downloads: 1}},
    new: true
}
```

Query attribute is the query to find out the document. Update is the statement to do changes on the document and new field is about the document returned. If new is true, the query is going to return the new version of the document, if new is false, it’s going to return the previous version of the document. By default new is configured to false.

There is another field called upsert which is going to create the document in case of the query returns no documents. It means no results query, will imply the creation of the new document with the information from query and update fields.

## Lesson 4: Updating MongoDB Documents by Using updateMany()

For updating many document is used the command updateMany(). It is created based on these documents

* Filter document. Contains the selection criteria.
* Update document. Contains the modification to apply to all documents which match the filter.
* Options object (optional)

At the end of ht operation is returned a summary of the items modified. Things to be considered with this operation:

* It’s not all-or-nothing operation. Operation would fail for any documents. In that scenario documents will not roll back updates. The rollback should be done manually by ourselves.
* Updates will be visible as soon as they are performed. That’s the reason this operation is not appropriate or some business use cases (example financial transactions).

Example `updateMany()` operation:

```mongodb-json
db.books.updateMany(
{ publishedDate: { $lt: new Date("2019-01-01") } },
{ $set: { status: "LEGACY" } }
)
```

Another example:

```mongodb-json
db.birds.updateMany(
{ $or: [{common_name: "Blue Jay"}, {common_name: "Grackle"}] },
{ $set: { last_seen: new Date("2022-01-01") } }
)
```

## Lesson 5: Deleting Documents in MongoDB

The command to delete documents, we have to do:

* `find()` - to find the document
* `deleteOne()` - to delete it

The typical way to delete one document is using the _id field to delete it. At the end the deleteOne(filter, options) operation is receiving a filter to delete the document.

In case we want to delete may documents, we could use deleteMany(filter, options) operation for it. It’s the same we have to provide a query to delete those documents.

In both cases it’s returned a summary of the operations (acknowledge and count)

Examples:

```mongodb-json
db.podcasts.deleteOne({ _id: Objectid("6282c9862acb966e76bbf20a") })
db.podcasts.deleteMany({category: “crime”})
```
