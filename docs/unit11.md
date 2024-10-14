# MongoDB Aggregation

## Lesson 1: Introduction to MongoDB Aggregation

Aggregation is an analysis and summary of data. A stage is an aggregation operation performed on the data. The aggregation pipeline is a series of stages completed one at a time in order. The aggregation pipeline is a framework for data aggregation.

We can execute one stage to retrieve some information needed. What is more interesting it the possibility to string together multiple stages to perform more complex operations (a pipeline).

A pipeline consist on multiple stages where data can be filtered, sorted, grouped and transformed. The aggregation pipeline is a powerful tool for data analysis. Output documents from one stage are input documents for the next stage.

In mongo Atlas there is aa UI to adding stages to a pipeline one by one. In order to get a deeper knowledge about aggregation we will use the CLI. Even this aggregation capbilities are available via Mongo Drivers.

Basic structure for aggregation is:

```json
db.collection.aggregate([
  { $stage_name: {<expression>} },
  { $stage_name: {<expression>} },
  { $stage_name: {<expression>} }
])
```

A stage is a single operation on the data. Most common stages are: 

1. `$match` - filters for data that matches a specific criteria
2. `$group` - groups documents based on a specific criteria
3. `$sort` - puts the documents in a specific order

The syntax for each stage depends on the stage itself. Documentation is the source to find out specific syntax for each stage.

Sometimes in stages fields are prefixed with a `$` sign. This is a way to reference fields in the documents. For example:

```json
$set: { defaultUserName: {
    $concat: ["$first_name", " ", "$last_name"]}
}
```

## Lesson 2: Using $match and $group Stages in a MongoDB Aggregation Pipeline

We will use `$match` to filter for documents that satisfy a query. We will use `$group` to group documents based on a specific criteria.

`$match` stage is used to filter documents that match a specific criteria. It receives one argument which is the query. Here an example based on zip codes from California: 

```json
db.zips.aggregate([
  { $match: { state: "CA" } }
])
```

This example works as a simple query. When we have `$match` stage, try to use it as early as possible in the pipeline so it can use indexes to filter the data and reduce the amount of data to be processed. Therefore, lessens processing required.

`$group` stage is used to group documents based on a group key. The output is one document for each unique value of the group key. The basic structure of this stage is:

```json
$group: {
  _id: <expression>, // Group key
  <field1>: { <accumulator1>: <expression1> },
  <field2>: { <accumulator2>: <expression2> }
}
```

Accumulators are operators that perform operations on the grouped data. The most common accumulators are:

1. `$sum` - sums the values of the field
2. `$avg` - calculates the average of the values of the field
3. `$first` - returns the first value of the field
4. `$last` - returns the last value of the field
5. `$max` - returns the maximum value of the field
6. `$min` - returns the minimum value of the field
7. `$push` - returns an array of all values of the field
8. `$addToSet` - returns an array of unique values of the field
9. `$stdDevPop` - calculates the population standard deviation of the values of the field
10. `$stdDevSamp` - calculates the sample standard deviation of the values of the field
11. `$count` - counts the number of documents in the group

For example, based on the zip codes collection, we want to group by city and count the amount of zip codes in each city:

```json 
db.zips.aggregate([
  { $group: { _id: "$city", totalZips: { $count: {} } } }
])
```

Making both operators working together, in the following example we are going to filter for zip codes in California and group by city:

```json
db.zips.aggregate([
  { $match: { state: "CA" } },
  { $group: { _id: "$city", totalZips: { $count: {} } } }
])
```

## Lesson 3: Using $sort and $limit Stages in a MongoDB Aggregation Pipeline

`$sort` and `$limit` stages can be combined together to retrieve top or bottom documents from a query.

`$sort` stage is used to sort all input documents and passes them through pipeline in sorted order. The basic structure of this stage is:

```json
db.collection.aggregate([
  { $sort: { field1: 1, field2: -1 } }
])
```

The value of 1 means ascending order and -1 means descending order. The values allowes to be sorted are numbers, strings, arrays, dates, etc..

Here an example about zip codes collection. In the following example zip codes are going to be sorted by population in descending order:

```json
db.zips.aggregate([
  { $sort: { pop: -1 } }
])
```

`$limit` stage is used to limit the number of documents that pass through the next aggregation stage. The basic structure of this stage is:

```json
db.collection.aggregate([
  {
    "$limit": <number_items>
  }
])
``` 

It has only a positive integer value which means the amount of documents to retain. Based on the previous example about population, we are going to display the top 5 zip codes by population:

```json
db.zips.aggregate([
  { $sort: { pop: -1 } },
  { $limit: 5 }
])
```

It's important to take into consideration that stages order is super important. In the previous example, if `$limit` stage is placed before `$sort` stage, the result will be the first 5 documents in the collection.

## Lesson 4: Using $project, $count, and $set Stages in a MongoDB Aggregation Pipeline

`$project` determines output shape of documents. It can be used to include, exclude, rename, or add fields. It's similar to projection done in `find()` method. It should be the last stage to format the output in the pipeline. The basic structure of this stage is:

```json
db.collection.aggregate([
  {
    "$project": {
      field1: <0/1/value>,
      field2: <0/1/value>,
      field3: <0/1/value>
    }
  }
])
```

The value of 1 means to include the field and 0 means to exclude the field. There is a way to provide a value to a new field using an expression. For example, to add a new field with the sum of two fields:

```json
db.zips.aggregate([
  {
    "$project": {
      _id: 0,
      zip: 1,
      state: 1,
      "population": "$pop",
      totalPop: {
        $add: [
          "$pop",
          "$pop"
        ]
      }
    }
  }
])
```

`$set` stage adds or modifies fields in the pipeline. Useful when we want to change existing fields in pipeline or add new ones to be used in upcoming stages. The basic structure of this stage is:

```json
db.collection.aggregate([
  {
    "$set": {
      field1: <expression>,
      field2: <expression>,
      field3: <expression>
    }
  }
])
```

For example, using the zip codes collection, we are going to add a new field about estimated population on 2030, multiplying the current population by 1.0031:

```json
db.zips.aggregate([
  {
    "$set": {
      pop_2030: { "$round": { $multiply: [ "$pop", 1.0031 ] } }
    }
  }
])
```

In this case, we have a new field called `pop_2030` with the estimated population in 2030.

Last stage is `$count` which counts the number of documents that pass through the stage. It returns the total documents count. The basic structure of this stage is:

```json
db.collection.aggregate([
  {
    "$count": "field_name"
  }
])
```

For example we want to count the amount of zip codes in the database:

```json
db.zips.aggregate([
  {
    "$count": "total_zips"
  }
])
```
It returns a single document with the total amount of zip codes in the collection.

Here a last example regarding a birds collection, filter them by species and between specific dates viewed:

```json
db.sightings.aggregate([
    {$match: {"species_common": "Eastern Bluebird", date: {$gte: ISODate('2022-01-01T00:00:00.000Z'), $lt: ISODate('2023-01-01T00:00:00.000Z') }}},
    {$count: "sightings"}
])
```

## Lesson 5: Using the $out Stage in a MongoDB Aggregation Pipeline

TBC































