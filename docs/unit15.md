# MongoDB Transactions

## Lesson 1: Introduction to ACID Transactions

When we have to transfer information from one document to another we should use a transaction to ensure both operations were done successfully. In this lesson, we will learn about ACID transactions and how they are implemented in MongoDB.

ACID transactions is the tool used by developers to solve the problem. ACID transactions are a group of database operations that will be completed as a unit or not at all. ACID is the acronym for Atomicity, Consistency, Isolation, and Durability.

* **Atomicity**: All operations in the transaction are completed successfully or none of them are.
* **Consistency**: The database is in a consistent state before and after the transaction. All changes made by operations are consistent with database constraints.
* **Isolation**: The transaction is isolated from other transactions. The changes made by the transaction are not visible to other transactions until the transaction is completed. Multiple transactions can happen at the same time with affecting the outcome of the other transactions.
* **Durability**: Once the transaction is completed, the changes are permanent and will not be lost. All of the changes that are made by operations in a transaction will persist, no matter what.

## Lesson 2: ACID Transactions in MongoDB

Due to the document database nature, single-document operations are already atomic in MongoDB. `updateOne` operation is an ACID transaction. If we launch an updateOne operation modifying several fields, all or none of them are going to be updated. 

Operation which involves several documents are not technically atomic and it will require extra steps to make it ACID transaction. 

The example provided in the course is about an ecommerce application which add one item to the shopping cart and reduce one from the inventory (both documents on different collections). Writing them with normal update operations is not atomic.

It will need an ACID transaction to be consistent.

In order to do a multi-document transaction, MongoDB "locks" resources involved in a transaction. It incurs in performance cost and affect latency. Multi-document transaction should be used as a precise tool, only in special circumstances only when it's needed. 

## Lesson 3: Using Transactions in MongoDB

First step is open a session. A session is used to group database operations that are related to each other and should be run together, similar to a transaction. A transaction has a maximum runtime of less than one minute after the first write. 

If we receive this kind of error: ```MongoServerError: Transaction 1 has been aborted ```, most likely, it'll be related with transaction timeout.

Steps: 

1. Create a session
2. Start a transaction. This operation doesn't show any output. It's normal.
3. Creating a constant to point to the account collection
4. Write first document decreasing balance
5. Write second document increasing balance
6. Commit the transaction. Both changes will be applied to the database.

```javascript
const session = db.getMongo().startSession();
session.startTransaction();
const account = session.getDatabase('bank').getCollection('account');
account.updateOne({account_id: 'MDB740740'}, {$inc: {balance: -30}});
account.updateOne({account_id: 'MDB987654'}, {$inc: {balance: 30}});
session.commitTransaction();
```

In case we don't want to commit the transaction we could roll back the transaction. This operation won't show any output. 

```javascript 
session.abortTransaction();
```

`commitTransaction` command is the only one showing output message about its completition. `startTransaction` and `abortTransaction` won't show any output.

