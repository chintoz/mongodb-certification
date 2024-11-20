# MongoDB Compass

## Learning Byte

Compass is the official graphical user interface (GUI) for MongoDB. It is a tool that allows us to interact with the database in a more visual way. It is a powerful tool that allows us to explore and manipulate data, create indexes, manage aggregations, and run queries.

In connections is shown a password placeholder to prevent showing it.

There is a functionality to import data from a file which could be a JSON, CSV, or TSV file. The schema tab analyze the schema analyzing types and values. 
We can create a JSON schema validation in Validation tab. We can define minimum mandatory fields to be inserted in a collection. We can add rules to the schema to validate the data. Those rules are defined in json schema format. We can define validation action in case of the rule is break and validation level could be strict or moderate. Strict means that the document will not be inserted if the rule is broken. Moderate means that the document will be inserted but a warning will be shown.

It provides a natural query bar which allows us to write queries in a more natural way. It also provides a visual query builder which allows us to build queries without writing any code. It also provides a visual explain plan which allows us to see how the query is executed.

There operations to do bulk delete or bulk updates on documents as result of a query.

Search bar allows to add conditions such as projection, sort, limit, skip, and collation. It also allows to add a filter to the query. 

There is a button called Insights on the search text are which shows warnings about that query, and it allows us to optimize the query. And there is a button called Explains to display the explain plan of the query.

Compass provides the UI to generate indexes.

We can build aggregations in Compass using Aggregations tab. It helps to generate aggretation pipelines with the Generate tool which is going to transform the natural language as an aggregation pipeline. 

In the example is used $topN operator to get the top N elements base on a sort criteria. 

We can save our aggregation pipelines to use them later. Those aggregations are shown in My Queries tab. Aggregation pipelines could be exported to a JSON file or any other programming language format.





