# Unit 3 - MongoDB and the Document Model


## Lesson 1 - Introduction to MongoDB

It’s a general purpose document database. It’s based on documents similar to JSON objects. Documents are flexible.**Document is the basic unit of data in MongoDB. Collection is a grouping of those documents. Database is a container for collections.**

Ideally, documents correspond with the data objects in the application. It could model data in any shape or structure. Data could be: key-value pairs, text, geospatial, time series, graph data, etc.
There are drivers implements for most important programming languages.

MongoDB could be used with different purposes: personal educational projects, start-ups, enterprise applications. Some cases of success are: e-commerce, content management, IOT and time series data, Trading and payment, gaming, mobile apps, Real time analytics and IA, etc.

The main features of MondoDB are related with:
* Scalability
* Resilience
* Speed of development
* Privacy and Security

The relationship between MongoDB and Atlas, **The MongoDB database is at the core of Atlas**. Atlas offer additional functionality such as: Full text search, Data visualization, build on top of MondoDB deployment.

Atlas is a developer data platform.

## Lesson 2 - The MongoDB document model

Document is the basic unit in MongoDB. Internally, we have Data Types to define document structure. Documents are flexible schema model.

Documents are displayed in a JSON format, but the are stored in a BSON format. BSON is an extension for Binary JSON, and MongoDB uses some features of this format to manage those documents.

BSON add supports for data types that are not available in JSON. Due to the usage of BSON, MongoDB support a wide range of data types:
* All JSON supported data types (such as: array, object, string, nulls bool ..)
* Dates
* Numbers
* ObjectIds
* etc.

ObjectID is a special data type to generate unique identifiers. Every document in the database requires a field called “_id” which acts as a primary key. If the inserted document doesn’t have that field, MondoDB will include it automatically generating an ObjectId value for it.

MongoDB supports a flexible data model, and polymorphic schema. It could follow the next:
* Documents may contain different fields
* Fields may contain different types

This flexible schema, allow the model to evolve quickly according to the necessities. In relational databases, it’s more complicated to change the schema, and it needed a set of DDL sentences to adjust it. In MongoDB it’s just a matter of adding the new field in our documents.

We could add additional optional schema validation, which are a set of constrains on the structure of the documents.


## Lesson 3 - Managing databases, collections, and documents in Atlas Data Explorer


Atlas Data Explorer is an UI provided by Atlas to interact and manage data. It could be used command line or Data Explorer UI. That UI offer us the possibility to create and view Databases, Collections and Documents.

There is a button to browse all collections. It will display all the databases in the cluster, and inside the database, all the collections inside the database. There is a view to display a document.

Capped collection or Time series collections?? Take a look on it in the documentation.

There is a UI to insert document field by field (as a table). And there is an option to insert it directly providing a JSON document.

It looks like in the UI the namespace search is going to search by database or collection name.
