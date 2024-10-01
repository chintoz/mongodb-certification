# Unit 2 - Getting started with MongoDB Atlas

## Lesson 1 - Introduction to MongoDB Atlas, the developer data platform

Atlas is a Database as a Service (DBaaS). You don’t need to worry about the platform. It’ll will handle all the details behind the scene.
By default, replication is enabled (replica set) and data is distributed in several nodes. Advantages:

* Data redundancy (performance)
* Data available in case of any disaster

There are two kind of deployments:

* Serverless -> Pay only for resources used. It’s good for unpredictable workload or for a fully serverless architecture.
* Cluster -> There are two kind of cluster deployments
  * Shared -> Smaller and existing free tier. 
  * Dedicated -> Customized for your needs. It’s the ideal configuration for production environment.

When a cluster is deployed, we can choose cloud provider: AWS, Azure, Google Cloud. It could be deployed in multiple regions and multiple clouds. We can change region, cloud provider or scale tier, and atlas is going to update it without any downtime.

Other features:

* Operational insights
* Backups with point-in-time restore.
* Online archive
* CLI and administration API

They said “Mongo Atlas is a comprehensive developer data platform” because there are several products an services than can be used by developers to be focused only in application development. Some of the are:

* Atlas search -> Provide rich search in the cloud
* Data device sync -> Mobile devices sync with cloud
* Data Lake -> Extract data for a cheaper structure called Data Lake. It could be formatted in an optimized way for analytical queries.
* Atlas Data Federation -> It allows aggregate queries from several Mongo Atlas, for Data Lakes and for you own object storage environment.
* Atlas Charts -> It allows you to create dashboard to visualize data.
* Data API -> It’s able to retrieve data from an API
* GraphQL API ->
* Functions and triggers -> To allow the creation Even Driven applications.

After quiz there was a statement which I failed:

* What can we do with MongoDB Atlas? Write and host a full application in a managed cloud environment (reason: Application Services are cloud services that simplify building applications with Atlas).

## Lesson 2 - Creating and deploying at Atlas Cluster

Organizations allow you to define group and define users and teams. We can use it to grant access to projects.
Projects define and organize resources like database clusters. Usual configuration is creating separate projects for development, testing and production environments.

To turn on backup of the cluster, it’s only available for M2 and above.

By default, mongo cluster is created without any user and without any IP address to allow access. First step will be create an administrative user. It could be created using username/password or it could be created with a certificate as credentials.

By default, mongodb blocks access from all IPs addresses except for Atlas itself. In order to access from another IP, it should be configured by the section to configure allowed IPs.

There is a mechanism to populate the database with a sample dataset.

Lab:

Command to create the cluster from command line:

```bash
atlas setup --clusterName myAtlasClusterEDU --provider AWS --currentIp --skipSampleData --username xxxxxx --password xxxxx | tee atlas_cluster_details.txt
```

Command to load dataset in the cluster:
```bash
atlas clusters sampleData load myAtlasClusterEDU
```

Quiz:

The default project created in Mongo Atlas is called Project0

First data cluster is created empty, therefore size is 0.


