
# db200-mongodb-workshop
In this Module, we learned the basics of installing mongoDB, and how to do some CRUDrat things with our friends.

# Special offers inside,  

# Author - Michel Roberts JR.

# Modified by Travis Ripley, * Started Monday April 29th, 2019 14:00

## Right after this commercial break:

## MongoDB Workshop

## Setup project files

1. Clone the repository from [github](https://github.com/SanDiegoCodeSchool/db200-mongodb-workshop)

### Local Installation

MacOS - use homebrew: [https://docs.mongodb.com/master/tutorial/install-mongodb-on-os-x/](https://docs.mongodb.com/master/tutorial/install-mongodb-on-os-x/)

Windows - use the installer from here: [https://docs.mongodb.com/master/tutorial/install-mongodb-on-windows/](https://docs.mongodb.com/master/tutorial/install-mongodb-on-windows/)

### Shell

The mongo shell is an interactive JavaScript interface to MongoDB.

First start a MongoDB instance using:
```
$ mongod
```

and then without closing that terminal, open another terminal instance and start the shell using:

```
$ mongo
```

It should connect to the default database by using the default database url and port.

Next, practice interacting with the shell by executing the following commands:

**NOTE: Type all these commands out - DO NOT copy/paste any of them. Practice typing them all so you can get a feel for the syntax, ensure you are not missing characters, and letting it sink in.**

To display the database you are using, type:
```
db
```
The operation should return test, which is the default database. To switch databases, try:
```
use <database>
```

To list the available databases, use:
```
show dbs
```

You can switch to non-existing databases. When you first store data in the database, such as by creating a collection, MongoDB creates the database. For example, the following creates both the database `mongo_workshop` and the collection `example` during the `insertOne()` operation:

```
use mongo_workshop
db.example.insertOne( { x: 1 } );
```
The db.example.insertOne() is just one of the methods available in the mongo shell.

### CRUD operations

You should be familiar with CRUD operations create, read, update, and delete.  MongoDB updates documents using different methods than SQL commands.  Here are the following SQL vs MongoDB equivalent commands:

* CREATE = INSERT

* READ = FIND

* UPDATE = UPDATE

* DELETE = DELETE (or REMOVE)

### Insert

Creating a new record in MongoDB can be done using the insert command:

```
db.collection.insert({ key: value })
```

using the mongo shell execute the following commands:

```
use mongo_workshop
db.cars.insert({ "make": "ford", "model":"explorer", "year": 2008 })
```

You will see a success message that confirms how many records have been written.  Note that you did not have to create a collection before adding a document.  MongoDb will create a collection for you, if it does not find one already exists. This can be both a blessing and a curse. If you misspell a collection or database name, you can end up creating new ones and adding documents there by mistake rather than adding a record to the existing db you had intended to use. 

Let's insert three more records, add the following items to the same collection:
```
db.cars.insert({ "make": "ford", "model":"fiesta", "year": 2012 })
db.cars.insert({ "make": "honda", "model":"civic", "year": 2015 })
db.cars.insert({ "make": "chevy", "model":"impala", "year": 1975, "options": { "radio": "8-track" } })
```

### Find

Now let's find the records you have added using:
```
db.cars.findOne({})
```
by passing an empty object, you will get back a random document from the collection.

To get back all documents try executing:

```
db.cars.find({})
```
Again notice that you get all results, but we can also query by passing a value. Assuming you want all ford's your query needs to be: { "make": "ford" } Try it, and see if you get back 2 records with:

```
db.cars.find({ "make": "ford" })
```

There are also methods you can use help refine the results including: to limit, sort, and pretty print the results:

* Limit to the first 3 records:

```
db.cars.find({}).limit(3)
```

* Sort in ascending order by issuing the following command in the shell:

```
db.cars.find({}).sort({"year": 1})
```

* Display results in an easy-to-read format by using:

```
db.cars.find({}).pretty()
```

#### Projection

In MongoDB the term projection refers to selecting or filtering some of the fields from results documents that would otherwise be returned by a query. To use a projection you will pass a second object in the find command, and set a flag of 1 or 0 for each field to indicate if you want the field to be included in results documents. If you do not specify, or do not provide a projection, by default MongoDB will return all matching documents with all the fields.

Use the following command to return a limited set of fields from the document:
```
db.cars.find({}, {"year":1, "make":1, _id:0})
```
You should get back several documents and each one will only contain the year and make in each document.

With the exception of the _id field, you may not have both inclusions and exclusions in the same projection document.

#### Comparison and Logical Operators

Often you will need to query and find records using additional methods like using the following object { "year": { $gt: 2000 } } will return all cars with a model year greater than 2000.

Other common comparison operators include:

* $eq - Matches values that are equal to a specified value.
* $gt - Matches values that are greater than a specified value.
* $gte - Matches values that are greater than or equal to a specified value.
* $in - Matches any of the values specified in an array.

Common logical operators include:

* $and - Joins query clauses with a logical AND returns all documents that match the conditions of both clauses.
* $not - Inverts the effect of a query expression and returns documents that do not match the query expression.

You can also combine comparison operators to specify ranges. The following operation returns documents with field between value1 and value2: `db.collection.find( { field: { $gt: value1, $lt: value2 } } );`

```
db.cars.find({ $where: "this.credits == this.debits" });
```


You can even evaluate some JavaScript expressions in MongoDb.  For example, the `$where` operator allows you to express a query where the built in operators do not provide the needed functionality.  For example:
```
db.collection.find( { $where: "this.credits == this.debits" } );
```
could be useful in an application where the object has known credit and debit values that must be compared to one another. Try running a few commands using `$where`.

Review all of the comparison operators [here](https://docs.mongodb.com/manual/reference/operator/query/index.html) before moving on to the next section.

### Update

Update operations modify existing documents in a collection. MongoDB provides the following methods to update documents of a collection:

```
db.collection.updateOne({query},{document})
db.collection.updateMany([{query},{document},{query},{document},...])
db.collection.replaceOne({query},{document})
```

Update the `ford` to be a `dodge` with:

```
db.cars.update({ "make": "ford" },{ "make": "dodge" })
```

Let's take a look at the document and verify it looks as expected using:

```
db.cars.find({ "make": "dodge" })
```

Probably not what you expected!? When you do an update it will replace the entire document contents unless you use `$set` for example: 

```
db.cars.updateOne({ "make": "dodge" },{ $set: { "model": "durango", "year": 2015 } })
```

This will update the document by adding/updating only the fields specified.

In MongoDB, update operations target a single collection. All write operations in MongoDB are atomic on the level of a single document.

### Delete

Delete (previously Remove) - Can be used to remove documents from a collection.

Let's remove that last document from the collection using:

```
db.cars.deleteOne({ "make": "dodge" })
```

then confirm with:

```
db.cars.find({ "make": "dodge" })
```

That covers basic CRUD operations, submit the tests and move on to the next project.

There is no need to publish this to now.sh and you can provide the github url only for this project submission. [Submit your project](https://goo.gl/forms/wx8DLSus7s88lk043) 

####

##
#Thank you for taking the time to look at my projects,

#Also please follow my progress on youtube: 
https://www.youtube.com/channel/UCXv4p-lDYeWXPlnoRFYCSUg