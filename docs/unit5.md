# Unit 5 - Connecting to a MongoDB database

## Lesson 1 - Using MongoDB connection string

We will have an URL to connect to our mongo. Connection could be done via shell, compass or any other application.
There are two URL formats:
Standard format - To connect to standalone clusters, replica sets o shared clusters.
DNS seed list format - Since Mongo 3.6. Provides a DNS server list to our connection string. Gives more flexibility of deployment. Ability to change servers in rotation without reconfiguring clients.

Basic format of connection string:
```bash
mongodb+srv://<username>:<password>@cluster0.usqsf.mongodb.net/?retryWrites=true&w=majority
```

If port is not specified it will use by default 27017.


# Lesson 2 - Connecting to a MongoDB Atlas cluster with the Shell

Mongo Shell is a NodeJS wrapper environment. Due to that fact we can create in Javascript: variables, functions, conditionals, loops, and control flow statements.

Apart from the “mongosh" command there is another one called “atlas”, and it could be connected to the cluster to display information about it.

The way to retrieve connection string is using

```bash
atlas cluster connectionStrings describe myAtlasClusterEDU
```

With “atlas cluster” we can show information about the structure of the command to be executed.

This command define a env variable with the connection string:

```bash
MY_ATLAS_CONNECTION_STRING=$(atlas clusters connectionStrings describe myAtlasClusterEDU | sed "1 d”)
mongosh -u myAtlasDBUser -p myatlas-001 $MY_ATLAS_CONNECTION_STRING
```

There is an existing command to show information about the role of the mongodb instance:

```$ db.hello()``` —> More information about the command: https://www.mongodb.com/docs/manual/reference/method/db.hello/

Which REPL environment does the MongoDB Shell use? Node

## Lesson 3 - Connecting to a MongoDB Atlas cluster with Compass.

MongoDB Compass is a GUI that allows to query and analyze our data, and compose aggregation pipelines.

Which tabs appear in MongoDB compass tool? My Queries, Performance, and Databases
Which describes MongoDb compass? GUI for querying, aggregating and analyzing data in MongoDB

## Lesson 4 - Connecting to a MongoDB Atlas cluster from an application.

MongoDB drivers connect our application to our database by using a connection string. More info about the drivers could be queries here:

http://mongodb.com/docs/drivers

It includes a wide variety of programming languages. If your language it’s not supported officially, it could be done using any driver implemented by the community.
Every documentation page per language contains: Quick start, Quick reference, Usage examples and Fundamentals sections.

MongoDB developer center has tutorials to integrate with MongoDB for a wide a variety of programming languages.
Apart from that, MongoDB university offers courses grouped by MongoDB driver (for instance java version).

## Lesson 5 - Troubleshooting MongoDB Atlas connection errors.

There are two common errors:

* Network access errors. When the IP is not allowed to connect, the connection take a while before failing. To fix it, we have to add the IP as the whitelist to access to the cluster. It is fixed in Network Access tab, and we have to add a new IP address to access to it. We can type it, we can add current IP, or we can allow access from anywhere. The configuration could be temporal, and we can limit it by a period of time (for instance, it’s valid for 6 hours). It needs 30 second to be propagated to our cluster.
* User authentication errors. If the credentials provided are not correct, we won’t be able to access to our data. The error is different, in this case the message is “Authentication failed”, and it takes shorter to fail. **MongoDB doesn’t autofill your password field in the connection string, so make sure the password is populated and correct before connecting.** 