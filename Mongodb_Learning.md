<h1 align="center">Mongo DB</h1>

## What is MongoDB?

`MongoDB` is a document database designed for ease of application development and scaling.

 + MongoDB offers both local and cloud-hosted deployment options:
     - For locally hosted deployments, MongoDB offers both a Community and an Enterprise version of the database:
        + MongoDB Community is the [source available and free](https://github.com/mongodb/mongo) to use edition of MongoDB.
        + MongoDB Enterprise is available as part of the MongoDB Enterprise Advanced subscription and includes comprehensive support for your MongoDB deployment. MongoDB Enterprise also adds enterprise-focused features such as LDAP and Kerberos support, on-disk encryption, and auditing.
     - [MongoDB Atlas](https://www.mongodb.com/cloud/atlas?tck=docs_server) is a hosted MongoDB Enterprise service option in the cloud which requires no installation overhead and offers a free tier to get started.

## Document Database

A record in MongoDB is a document, which is a data structure composed of field and value pairs. MongoDB documents are similar to JSON objects. The values of fields may include other documents, arrays, and arrays of documents.

### The advantages of using documents are:

 + Documents (i.e. objects) correspond to native data types in many programming languages.
 + Embedded documents and arrays reduce need for expensive joins.
 + Dynamic schema supports fluent polymorphism.

### Collections/Views/On-Demand Materialized Views

 + MongoDB stores documents in collections. Collections are analogous to tables in relational databases.
 + In addition to collections, MongoDB supports:
     - Read-only Views (Starting in MongoDB 3.4)
     - On-Demand Materialized Views (Starting in MongoDB 4.2).

## Getting started

 + switch DB
    - `db` refers to your current database
    - To switch databases, type `use <db>`
 + Insert 
    -  If a collection does not exist, MongoDB creates the collection when you first store data for that collection.
    - uses the `db.<collection>.insertMany()` method to insert new documents
 + Select 
    - To select the documents from a collection, you can use the `db.<collection>.find()` method. 
    - To select all documents in the collection, pass an empty document as the query filter document to the method.
 + Specify Fields to Return (Projection)
    - To specify fields to return, pass a projection document to the `db.collection.find(<query document>, <projection document>)` method. In the projection document, specify:
        + `<field>: 1`  to include a field in the returned documents
        + `<field>: 0`  to exclude a field in the returned documents
    - Eg: `db.movies.find( { }, { "title": 1, "directors": 1, "year": 1 } );`
    - You do not have to specify the _id field to return the field. It returns by default. To exclude the field, set it to 0 in the projection document. For example, run the following query to return only the title, and the genres fields in the matching documents:
        + Eg: `db.movies.find( { }, { "_id": 0, "title": 1, "genres": 1 } );`

 + Aggregate Data ($group)
    - You can use aggregation to group values from multiple documents together and return a single result. Aggregation in MongoDB is performed with an aggregation pipeline.
    - While find() operations are useful for data retrieval, the aggregation pipeline allows you to manipulate data, perform calculations, and write more expressive queries than simple CRUD operations.
    - Eg:
        ```javascript
        db.movies.aggregate( [
       { $unwind: "$genres" },
       {
         $group: {
           _id: "$genres",
           genreCount: { $count: { } }
         }
       },
       { $sort: { "genreCount": -1 } }
        ] )
        ```
    
    - The pipeline uses:
        + `$unwind` to output a document for each element in the genres array.
        + `$group` and the $count accumulator to count the number of occurrences of each genre. This value is stored in the genreCount field.
        + `$sort` to sort the resulting documents by the genreCount field in descending order.
    
## Databases and Collections

### Overview
MongoDB stores data records as documents (specifically BSON documents) which are gathered together in collections. A database stores one or more collections of documents.

### Databases
In MongoDB, databases hold one or more collections of documents. To select a database to use, in mongosh, issue the `use <db>` statement, as in the following example:

Eg: `use myDB`

#### Create a Database
If a database does not exist, MongoDB creates the database when you first store data for that database. As such, you can switch to a non-existent database and perform the following operation in mongosh:

```javascript
use myNewDB
db.myNewCollection1.insertOne( { x: 1 } )
```
The `insertOne()` operation creates both the database myNewDB and the collection myNewCollection1 if they do not already exist. Be sure that both the database and collection names follow MongoDB Naming Restrictions.

### Collections
MongoDB stores documents in collections. Collections are analogous to tables in relational databases.

#### Create a Collection

If a collection does not exist, MongoDB creates the collection when you first store data for that collection.
```
db.myNewCollection2.insertOne( { x: 1 } )
db.myNewCollection3.createIndex( { y: 1 } )
```
Both the insertOne() and the createIndex() operations create their respective collection if they do not already exist.

#### Explicit Creation

MongoDB provides the `db.createCollection()` method to explicitly create a collection with various options, such as setting the maximum size or the documentation validation rules. If you are not specifying these options, you do not need to explicitly create the collection since MongoDB creates new collections when you first store data for the collections.

To modify these collection options, see [collMod](https://docs.mongodb.com/manual/reference/command/collMod/#mongodb-dbcommand-dbcmd.collMod).

#### Document Validation

By default, a collection does not require its documents to have the same schema; i.e. the documents in a single collection do not need to have the same set of fields and the data type for a field can differ across documents within a collection.

Starting in MongoDB 3.2, however, you can enforce document validation rules for a collection during update and insert operations. See [Schema Validation](https://docs.mongodb.com/manual/core/schema-validation/) for details.

#### Unique Identifiers
New in version 3.6.

Collections are assigned an immutable UUID. The collection UUID remains the same across all members of a replica set and shards in a sharded cluster.

To retrieve the UUID for a collection, run either the `listCollections` command or the `db.getCollectionInfos()` method.

### Views

A MongoDB view is a queryable object whose contents are defined by an aggregation pipeline on other collections or views. MongoDB does not persist the view contents to disk. A view's content is computed on-demand when a client queries the view. MongoDB can require clients to have permission to query the view. MongoDB does not support write operations against views.

#### Create View

To create or define a view:

 + Use the `db.createCollection()` method or the create command:
    ```js
    db.createCollection(
      "<viewName>",
      {
        "viewOn" : "<source>",
        "pipeline" : [<pipeline>],
        "collation" : { <collation> }
      }
    )
    ```

 + Use the db.createView() method:
    ```js
    db.createView(
      "<viewName>",
      "<source>",
      [<pipeline>],
      {
        "collation" : { <collation> }
      }
    )
    ```
```
NOTE: 
    You must create views in the same database as the source collection.
The view definition pipeline cannot include the $out or the $merge stage. If the view definition includes nested pipeline (e.g. the view definition includes $lookup or $facet stage), this restriction applies to the nested pipelines as well.
```

#### Supported Operations

| Commands | Methods |
| :--- | :--- |
| create | db.createCollection() <br> db.createView() |
| collMod | db.getCollection()<br> db.getCollectionInfos() <br>  db.getCollectionNames() |
| find <br> distinct <br> count | db.collection.aggregate() <br> db.collection.find() <br> db.collection.findOne() <br> db.collection.countDocuments() <br> db.collection.estimatedDocumentCount() <br> db.collection.count() <br> db.collection.distinct()

#### On-Demand Materialized Views

Starting in version 4.2, MongoDB adds the $merge stage for the aggregation pipeline. This stage can merge the pipeline results to an existing collection instead of completely replacing the collection. This functionality allows users to create on-demand materialized views, where the content of the output collection can be updated each time the pipeline is run.

 1. Define the On-Demand Materialized View
The following updateMonthlySales function defines a monthlybakesales materialized view that contains the cumulative monthly sales information. In the example, the function takes a date parameter to only update monthly sales information starting from a particular date.
    ```js
    updateMonthlySales = function(startDate) {
       db.bakesales.aggregate( [
          { $match: { date: { $gte: startDate } } },
          { $group: { _id: { $dateToString: { format: "%Y-%m", date: "$date" } }, sales_quantity: { $sum:   "$quantity"}, sales_amount: { $sum: "$amount" } } },
          { $merge: { into: "monthlybakesales", whenMatched: "replace" } }
       ] );
    };
    ```

    - The `$match` stage filters the data to process only those sales greater than or equal to the startDate.
    - The `$group` stage groups the sales information by the year-month. The documents output by this stage have the form:
        ```js
        { "_id" : "<YYYY-mm>", "sales_quantity" : <num>, "sales_amount" : <NumberDecimal> }
        ```
    - The $merge stage writes the output to the monthlybakesales collection.
    - Based on the _id field (the default for unsharded output collections), the stage checks if the document in the aggregation results matches an existing document in the collection:
        + When there is a match (i.e. a document with the same year-month already exists in the collection), the stage replaces the existing document with the document from the aggregation results.
        + When there is not a match, the stage inserts the document from the aggregation results into the collection (the default behavior when not matched).
2. Perform Initial Run

    For the initial run, you can pass in a date of `new ISODate("1970-01-01")`:

    updateMonthlySales(new ISODate("1970-01-01"));

    After the initial run, the monthlybakesales contains the following documents; i.e. `db.monthlybakesales.find().sort( { _id: 1 } )` returns the following:
    ```js
    { "_id" : "2018-12", "sales_quantity" : 41, "sales_amount" : NumberDecimal("506") }
    { "_id" : "2019-01", "sales_quantity" : 86, "sales_amount" : NumberDecimal("896") }
    ```
3. Refresh Materialized View
    Assume that by the first week in February 2019, the bakesales collection is updated with newer sales information; specifically, additional January and February sales.
    ```js
    db.bakesales.insertMany( [
       { date: new ISODate("2019-01-28"), item: "Cake - Chocolate", quantity: 3, amount: new NumberDecimal("90")    },
       { date: new ISODate("2019-01-28"), item: "Cake - Peanut Butter", quantity: 2, amount: new NumberDecimal  ("32") },
       { date: new ISODate("2019-01-30"), item: "Cake - Red Velvet", quantity: 1, amount: new NumberDecimal ("20") },
       { date: new ISODate("2019-01-30"), item: "Cookies - Chocolate Chip", quantity: 6, amount: new    NumberDecimal("24") },
       { date: new ISODate("2019-01-31"), item: "Pie - Key Lime", quantity: 2, amount: new NumberDecimal("40") },
       { date: new ISODate("2019-01-31"), item: "Pie - Banana Cream", quantity: 2, amount: new NumberDecimal    ("40") },
       { date: new ISODate("2019-02-01"), item: "Cake - Red Velvet", quantity: 5, amount: new NumberDecimal ("100") },
       { date: new ISODate("2019-02-01"), item: "Tarts - Apple", quantity: 2, amount: new NumberDecimal("8") },
       { date: new ISODate("2019-02-02"), item: "Cake - Chocolate", quantity: 2, amount: new NumberDecimal("60")    },
       { date: new ISODate("2019-02-02"), item: "Cake - Peanut Butter", quantity: 1, amount: new NumberDecimal  ("16") },
       { date: new ISODate("2019-02-03"), item: "Cake - Red Velvet", quantity: 5, amount: new NumberDecimal ("100") }
    ] )
    ```
    To refresh the monthlybakesales data for January and February, run the function again to rerun the  aggregation pipeline, starting with new ISODate("2019-01-01").

    `updateMonthlySales(new ISODate("2019-01-01"));`

    The content of monthlybakesales has been updated to reflect the most recent data in the bakesales   collection; i.e. db.monthlybakesales.find().sort( { _id: 1 } ) returns the following:
    ```js
    { "_id" : "2018-12", "sales_quantity" : 41, "sales_amount" : NumberDecimal("506") }
    { "_id" : "2019-01", "sales_quantity" : 102, "sales_amount" : NumberDecimal("1142") }
    { "_id" : "2019-02", "sales_quantity" : 15, "sales_amount" : NumberDecimal("284") }
    ```
+ Additional Information

    - The $merge stage:
        + Can output to a collection in the same or different database.
        + Creates a new collection if the output collection does not already exist.
        + Can incorporate results (insert new documents, merge documents, replace documents, keep existing documents, fail the operation, process documents with a custom update pipeline) into an existing collection.
        + Can output to a sharded collection. Input collection can also be sharded.

### Capped Collections

Capped collections are fixed-size collections that support high-throughput operations that insert and retrieve documents based on insertion order. Capped collections work in a way similar to circular buffers: once a collection fills its allocated space, it makes room for new documents by overwriting the oldest documents in the collection.

#### Create a Capped Collection
You must create capped collections explicitly using the db.createCollection() method, which is a mongosh helper for the create command. When creating a capped collection you must specify the maximum size of the collection in bytes, which MongoDB will pre-allocate for the collection. The size of the capped collection includes a small amount of space for internal overhead.

`db.createCollection( "log", { capped: true, size: 100000 } )`

If the size field is less than or equal to 4096, then the collection will have a cap of 4096 bytes. Otherwise, MongoDB will raise the provided size to make it an integer multiple of 256.

Additionally, you may also specify a maximum number of documents for the collection using the max field as in the following document:

`db.createCollection("log", { capped : true, size : 5242880, max : 5000 } )`

#### Query a Capped Collection
If you perform a find() on a capped collection with no ordering specified, MongoDB guarantees that the ordering of results is the same as the insertion order.

To retrieve documents in reverse insertion order, issue find() along with the sort() method with the $natural parameter set to -1, as shown in the following example:

`db.cappedCollection.find().sort( { $natural: -1 } )`

#### Check if a Collection is Capped

Use the isCapped() method to determine if a collection is capped, as follows:

`db.collection.isCapped()

#### Convert a Collection to Capped

You can convert a non-capped collection to a capped collection with the convertToCapped command:

`db.runCommand({"convertToCapped": "mycoll", size: 100000});`

The size parameter specifies the size of the capped collection in bytes.

This holds a database exclusive lock for the duration of the operation. Other operations which lock the same database will be blocked until the operation completes. See What locks are taken by some common client operations? for operations that lock the database.

### Time Series Collection

Time series collections efficiently store sequences of measurements over a period of time. Time series data is any data that is collected over time and is uniquely identified by one or more unchanging parameters. The unchanging parameters that identify your time series data is generally your data source's metadata.

## Documents
MongoDB stores data records as BSON documents. BSON is a binary representation of JSON documents, though it contains more data types than JSON. 

### Document Structure
MongoDB documents are composed of field-and-value pairs and have the following structure:
```json
{
   field1: value1,
   field2: value2,
   field3: value3,
   ...
   fieldN: valueN
}
```

#### Field Names
Field names are strings.

Documents have the following restrictions on field names:

 - The field name _id is reserved for use as a primary key; its value must be unique in the collection, is immutable, and may be of any type other than an array. If the _id contains subfields, the subfield names cannot begin with a ( \$ ) symbol.
 - Field names cannot contain the null character.
 - The server permits storage of field names that contain dots (.) and dollar signs (\$).
 - MongodB 5.0 adds improved support for the use of (\$) and (.) in field names. There are some restrictions. See Field Name Considerations for more details.

### Dot Notation
MongoDB uses the dot notation to access the elements of an array and to access the fields of an embedded document.

#### Arrays
To specify or access an element of an array by the zero-based index position, concatenate the array name with the dot (.) and zero-based index position, and enclose in quotes:

`"<array>.<index>"`

### Document Limitations
Documents have the following attributes:

#### Document Size Limit
The maximum BSON document size is 16 megabytes.

The maximum document size helps ensure that a single document cannot use excessive amount of RAM or, during transmission, excessive amount of bandwidth. To store documents larger than the maximum size, MongoDB provides the GridFS API. See mongofiles and the documentation for your driver for more information about GridFS.

#### Document Field Order
Unlike JavaScript objects, the fields in a BSON document are ordered.

#### Field Order in Queries
For queries, the field order behavior is as follows:

 + When comparing documents, field ordering is significant. For example, when comparing documents with fields a and b in a query:
    - {a: 1, b: 1} is equal to {a: 1, b: 1}
    - {a: 1, b: 1} is not equal to {b: 1, a: 1}
 + For efficient query execution, the query engine may reorder fields during query processing. Among other cases, reordering fields may occur when processing these projection operators: $project, $addFields, $set, and $unset.
    - Field reordering may occur in intermediate results as well as the final results returned by a query.
    - Because some operations may reorder fields, you should not rely on specific field ordering in the results returned by a query that uses the projection operators listed earlier.
#### Field Order in Write Operations
For write operations, MongoDB preserves the order of the document fields except for the following cases:
 + The _id field is always the first field in the document.
 + Updates that include renaming of field names may result in the reordering of fields in the document.

## BSON Types
BSON is a binary serialization format used to store documents and make remote procedure calls in MongoDB. 

Each BSON type has both integer and string identifiers as listed in the following table:

| Type | Number | Alias | Notes |
| --- | --- | --- | --- |
| Double | 1 | "double" |  | 
| String | 2 | "string" |  |
| Object | 3 | "object" |  | 
| Array | 4 | "array" |  | 
| Binary data | 5 | "binData" | | 
| Undefined | 6 | "undefined" | Deprecated.|
| ObjectId | 7 | "objectId" |  | 
| Boolean | 8 | "bool" |  | 
| Date | 9 | "date" |  | 
| Null | 10 | "null" |  | 
| Regular Expression | 11 | "regex" |  | 
| DBPointer | 12 | "dbPointer" | Deprecated. |
| JavaScript | 13 | "javascript" |  | 
| Symbol | 14 | "symbol" | Deprecated. |
| JavaScript code with scope | 15 | "javascriptWithScope" | Deprecated in MongoDB 4.4. |
| 32-bit integer | 16 | "int" |  | 
| Timestamp | 17 | "timestamp" |  | 
| 64-bit integer | 18 | "long" |  | 
| Decimal128 | 19 | "decimal" | New in version 3.4. | 
| Min key | -1 | "minKey" |  | 
| Max key | 127 | "maxKey" |  | 

 + The $type operator supports using these values to query fields by their BSON type. $type also supports the number alias, which matches the integer, decimal, double, and long BSON types.
 + The $type aggregation operator returns the BSON type of its argument.
 + The $isNumber aggregation operator returns true if its argument is a BSON integer, decimal, double, or long. New in version 4.4

#### Comparison/Sort Order

When comparing values of different BSON types, MongoDB uses the following comparison order, from lowest to highest

## MongoDB CRUD Operations

CRUD operations create, read, update, and delete documents.

### Insert Methods

| Method | Usage |
| --- | --- |
| db.collection.insertOne() | Inserts a single document into a collection. |
| db.collection.insertMany() | db.collection.insertMany() inserts multiple documents into a collection. |
| db.collection.insert() | db.collection.insert() inserts a single document or multiple documents into a collection. |

#### Additional Methods for Inserts

The following methods can also add new documents to a collection:

 + `db.collection.update()` when used with the upsert: true option.
 + `db.collection.updateOne()` when used with the upsert: true option.
 + `db.collection.updateMany()` when used with the upsert: true option.
 + `db.collection.findAndModify()` when used with the upsert: true option.
 + `db.collection.findOneAndUpdate()` when used with the upsert: true option.
 + `db.collection.findOneAndReplace()` when used with the upsert: true option.
 + `db.collection.bulkWrite()`.

 ➤ Query an Array

 Match an Array

To specify equality condition on an array, use the query document { `<field>: <value>` } where `<value>` is the exact array to match, including the order of the elements.

The following example queries for all documents where the field tags value is an array with exactly two elements, "red" and "blank", in the specified order:

const cursor = db.collection('inventory').find({
  tags: ['red', 'blank']
});


If, instead, you wish to find an array that contains both the elements "red" and "blank", without regard to order or other elements in the array, use the $all operator:

const cursor = db.collection('inventory').find({
  tags: { $all: ['red', 'blank'] }
});

#### Query for an Array Element that Meets Multiple Criteria

Use `$elemMatch` operator to specify multiple criteria on the elements of an array such that at least one array element satisfies all the specified criteria.

Use the `$size` operator to query for arrays by number of elements.

### Update Documents

 + Collection.updateOne()
 + Collection.updateMany()
 + Collection.replaceOne()

#### The update operation:

 + uses the $set operator to update the value of the size
 + uses the $currentDate operator to update the value of the lastModified field to the current date. If lastModified field does not exist, $currentDate will create the field. See $currentDate for details.

| Method | Use |
| --- | --- |
| db.collection.updateOne() | Updates at most a single document that match a specified filter even though multiple documents may match the specified filter. |
| db.collection.updateMany() | Update all documents that match a specified filter. |
| db.collection.replaceOne() | Replaces at most a single document that match a specified filter even though multiple documents may match the specified filter. |
| db.collection.update() | Either updates or replaces a single document that match a specified filter or updates all documents that match a specified filter. <br> By default, the db.collection.update() method updates a single document. To update multiple documents, use the multi option. |

Additional Methods

The following methods can also update documents from a collection:

 + db.collection.findOneAndReplace().
 + db.collection.findOneAndUpdate().
 + db.collection.findAndModify().
 + db.collection.bulkWrite().

### Delete Documents

| Method | Use |
| db.collection.deleteOne() | Delete at most a single document that match a specified filter even though multiple documents may match the specified filter. |
| db.collection.deleteMany() | Delete all documents that match a specified filter. |
| db.collection.remove() | Delete a single document or all documents that match a specified filter.|

Additional Methods

The following methods can also delete documents from a collection:

db.collection.findOneAndDelete().

findOneAndDelete() provides a sort option. The option allows for the deletion of the first document sorted by the specified order.

db.collection.findAndModify().

db.collection.findAndModify() provides a sort option. The option allows for the deletion of the first document sorted by the specified order.

db.collection.bulkWrite().

### Bulk Write Operations

MongoDB provides clients the ability to perform write operations in bulk. Bulk write operations affect a single collection. MongoDB allows applications to determine the acceptable level of acknowledgement required for bulk write operations.

#### bulkWrite() Methods

bulkWrite() supports the following write operations:

 + insertOne
 + updateOne
 + updateMany
 + replaceOne
 + deleteOne
 + deleteMany

Eg :

```json
try {
   db.characters.bulkWrite(
      [
         { insertOne :
            {
               "document" :
               {
                  "_id" : 4, "char" : "Dithras", "class" : "barbarian", "lvl" : 4
               }
            }
         },
         { insertOne :
            {
               "document" :
               {
                  "_id" : 5, "char" : "Taeln", "class" : "fighter", "lvl" : 3
               }
            }
         },
         { updateOne :
            {
               "filter" : { "char" : "Eldon" },
               "update" : { $set : { "status" : "Critical Injury" } }
            }
         },
         { deleteOne :
            { "filter" : { "char" : "Brisbane" } }
         },
         { replaceOne :
            {
               "filter" : { "char" : "Meldane" },
               "replacement" : { "char" : "Tanys", "class" : "oracle", "lvl" : 4 }
            }
         }
      ]
   );
}
catch (e) {
   print(e);
}
```

The operation returns the following:

```
{
   "acknowledged" : true,
   "deletedCount" : 1,
   "insertedCount" : 2,
   "matchedCount" : 2,
   "upsertedCount" : 0,
   "insertedIds" : {
      "0" : 4,
      "1" : 5
   },
   "upsertedIds" : {
   }
}
```

### SQL to MongoDB Mapping Chart

| SQL Terms/Concepts | MongoDB Terms/Concepts |
| --- | --- |
| database | database | 
| table | collection | 
| row | document or BSON document | 
| column | field | 
| index | index | 
| table joins | $lookup, embedded documents | 
| primary key <br> Specify any unique column or column combination as primary key. | primary key <br> In MongoDB, the primary key is automatically set to the _id field. |
| aggregation (e.g. group by) | aggregation pipeline <br> See the SQL to Aggregation Mapping Chart. | 
| SELECT INTO NEW_TABLE | $out <br> See the SQL to Aggregation Mapping Chart. | 
| MERGE INTO TABLE | $merge (Available starting in MongoDB 4.2) | 
| UNION ALL | $unionWith (Available starting in MongoDB 4.4) | 
| transactions | transactions | 


#### Executables

The following table presents some database executables and the corresponding MongoDB executables. This table is not meant to be exhaustive.

|  | MongoDB | MySQL | Oracle | Informix | DB2 |
| --- |  --- | --- |  --- |  --- |  --- | 
| Database Server |  mongod | mysqld | oracle | IDS | DB2 Server |  
| Database Client |  mongosh |  mysql |  sqlplus |  DB-Access |  DB2 Client |  

### text Search

#### $text Operator
Use the $text query operator to perform text searches on a collection with a text index.

Eg: `db.stores.find( { $text: { $search: "java coffee shop" } } )`

## MongoDB CRUD Concepts

### Analyze Query Performance

Use `explain()` method to know the perfpmance
```js
db.inventory.find(
   { quantity: { $gte: 100, $lte: 200 } }
).explain("executionStats")
```

## Cheat Sheets

 + [Aggregation cheatsheet](https://www.practical-mongodb-aggregations.com/appendices/cheatsheet.html) (4 out of 5)

 