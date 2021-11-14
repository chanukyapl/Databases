<h1 align="center">MONGO DB</h1>

## Introducing MongoDB

MongoDB is a leading NoSQL, classified as a document-oriented database. The word MongoDB is originated from humongous, meaning huge.

 + MongoDB is written in C++ language.
 + MongoDB pairs each key with a complex data structure named as document.
 + MongoDB stores document in a binary-encoded format termed as BSON. BSON is an extended format of JSON data model.

### NoSQL - an Overview

NoSQL provides a mechanism for retrieval and storage of data other than relational databases. The general observations of NoSQL are:

 + NoSQL does not use the relational model.
 + NoSQL runs on clusters.
 + NoSQL is probably open-source.
 + NoSQL is mostly used in Big data and Real-time web applications.
 + The commonly used Data Structures are Document, Graph, Key-value, and Wide column.

### What are Document Databases?
Document-oriented databases are a special type of NoSQL database used for
 + storing
 + retrieving
 + managing semi-structured data.

Document databases pair each key with a complex data structure commonly with a block of XML or JSON termed as a document.

Some of the commonly used Document Databases are MongoDB, Couchbase, OrientDB, and RavenDB.

### BSON - an Overview
BSON can be defined as a bin­ary-en­coded seri­al­iz­a­tion of JSON.

BSON supports embedding objects and arrays within other objects and arrays.

MongoDB can even 'reach inside' BSON objects to build indexes and match objects against query expressions on both top-level and nested BSON keys.

Example:

Suppose we have a document that contains {"Study":"MongoDB"}. It will be stored in BSON format as shown below:
```
 \x32\x00\x00\x00     //document size
 \x06                 //String Types
 Study\x00            //name of field 
\x16\x00\x00\x00MongoDB\x00  //value 
 \x00                   // Ending 
 ```

#### Characteristics of BSON
Traversable: BSON can be traversed very easily.

Efficient: Due to the usage of C data types, en­cod­ing and de­cod­ing from BSON can be per­formed accurately in most lan­guages.

## Why MongoDB?

MongoDB is recommended:

 + where very minimal Total Cost of Ownership (TCO) is required.
 + when a need for replication across multiple data centers globally.
 + where rapid deployment and faster scaling are required.
 + when a need for easy loading of data at the beginning and overtime is needed.
 + when massive concurrency is demanded by a user.
 + when no downtime can be tolerated.
 + when the database needs to grow rapidly as per user needs.
 + when high uncertainty in sizing exists.
 + where seamless and consistent experience is expected.

## What it is not !
Let's understand where Mongo will not fit in.

 + MongoDB does not compete with SAS and SPSS as an analytical suite.
 + MongoDB does not compete with Teradata, Netezza, Vertica as a data warehouse technology.
 + MongoDB is not a BI tool competing with Tableau or Quick view for Back Office transaction processing.
 + MongoDB does not compete with IBM Mainframes as a backend for a billing system or general ledger system.
 + MongoDB does not compete with Elastic search, SOLR as a search engine.

## Features of MONGO DB

### Indexing

Following are the indexes supported in MongoDB.

 + default_id: Each collection contains an index named default_id.
 + Single Field: Used for Single field or sort. Indexes can be either in ascending order or descending order.
 + Compound Index: Used for multiple fields.
 + Multikey Index: These are used to index array data.
 + Geo-spacial Index: Indexes used are two dimensional and 2D sphere (geolocation).

### Load Balancing
Sharding is a technique used for distributing data across multiple servers.

 + MongoDB supports Horizontal scaling by sharding.
 + Mongo leverages Sharding for splitting up of a large collection among multiple servers.
 + MongoDB supports deployments with very large data sets and high throughput operations through this.

### Capped Collections

MongoDB supports Capped collections. It is a fixed type collection that maintains insertion order once the specified size has arrived.

It acts as a circular queue. In this collection, you can restrain the size of the collection, or you can put a limit on the size of a collection.

Syntax:

`db.createCollection(<CollectionName>, {capped: <true/false>, size: Number, max:number })`

Two types of capped collections are used as given below:
 + fs.files is used to store the metadata.
 + fs.chunks is used to store the file chunks.

Example:

Suppose we have an eCommerce application. We are logging user data and should restrict data not to go more than four documents.

In such scenario, we use capped collection.
```js
db.createCollection("LogUsers",
 {capped : true,size : 100, max :4})
```

### Adhoc Querying
MongoDB supports

 + Single value field,
 + Range fields,
 + Conditional operators,
 + Regular expression search queries.

### Replication
MongoDB uses replica sets for high availability.

Replica sets contain two or more copies of the data. Each replica set may act as a primary or secondary replica set. 

By default, read and write operations are performed on the primary replica. The secondary replica will maintain a copy of primary data.

### Storage Mechanisms
#### MongoDB supports different storage engines:

 + MMAPv1: Default Storage engine till MongoDB version 3.2.
 + WiredTiger: Default storage engine starting from MongoDB 3.2.
 + In-Memory Storage Engine: This storage engine will be available in Enterprise version. It retains documents in-memory.

#### MongoDB uses GridFS specification for storing and retrieving large collections.

GridFS is a special type of file system in which data can be stored within MongoDB collections. GridFS splits a larger file into smaller chunks and stores each chunk of data in a separate document with a size of 255k.

### Aggregation
In MongoDB, aggregation process records and return computed results.

Aggregation can be categorized as :

 + Pipeline Aggregation: Documents are piped through processing pipeline and executes in different stages and transforms the documents into a final aggregated result.
 + Map-Reduce: It splits a larger problem into smaller chunks and sends to different machines for processing. It comprises two phases: reduce and map.
 + Single Purpose: These operations will aggregate documents from a single collection.

## Architecture

<img src=".\public\Mongodb_architecture.jpeg" alt="high level architecture">

The architecture of MongoDB comprises:

 + Application Driver
    - Drivers are client libraries that provide interfaces and methods for applications to interact with MongoDB database.
    - Drivers will handle the translation of documents between BSON objects and mapping structures.
    - C++, Java, .NET, Ruby, JavaScript, Node.js, Python, Perl, PHP, and Scala are some of the widely used drivers supported by MongoDB.
 + Databases
    - The database can be defined as a physical container of collections. MongoDB server can have one or more databases.
    - The default database for MongoDB is test. In the absence of any database, collections will be stored in the test database.
    - The command to check databases in MongoDB Server:
        `show dbs`
 + Collections
    - A collection consists of a group of MongoDB documents. It is similar to RDBMS table. Documents inside a collection can have same or different fields.
 + Documents
    - A document is a set of key-value pairs that support dynamic schema. A document is similar to Row in RDBMS. In Relational databases, schemas should be defined before we add any data whereas MongoDB allows the insertion of data without a predefined schema.
    - Dynamic schema implies that the documents stored in the database can have different fields, with different types for each field.
 + Indexes
    - In MongoDB, indexes support fast and efficient execution of queries.
    - In the absence of indexes, MongoDB will scan every document in a collection to select those documents that match the query statement. If an index exists, the number of documents to inspect can be restricted.
 + Security Features
    - MongoDB recommends various security features to protect MongoDB deployment. They are:
        + Authentication
        + Authorization
        + Encryption on data
        + Auditing
        + Hardening - (Ensure only trusted hosts have access)
 + Storage Engine
    - Storage engine is a part of the database that manages how data should be stored in memory and on disk. Storage engine can remarkably impact the performance of the applications.
    - It acts as an interface between persistent storage, i.e., disk and MongoDB database. MongoDB supports different storage engines:
        + MMAPv1
            - MMAPv1 is a traditional storage engine based on memory mapped files. This storage engine provides better workloads with high volume reads, inserts and in-place updates.
            - MMAPv1 automatically allocates power-of-two-sized documents when new documents are inserted.
            - Operating system decides which pages can fit into memory.
            - It supports two types of strategies.
                1. Power-of-two allocation: Store documents in power-of-two, eg: 32, 64, 128, 256, 512 … 2 MB. It works more efficiently for more insert, update delete workloads.
                2. Extra Fit: Collections whose workloads that consist of insert-only operations, or update operations will not increase document size.
            - Consistency in this storage is achieved through Journalling. MongoDB writes journal files every 100 milliseconds and writes data files to disk on every 60 seconds.
            - Indexes and Data memories are mapped into virtual address space.
            - Frequently used pages will get retained in RAM.
            - Limitations of MMAPv1 Storage Engine:
                + MMAPV1 offers collection level locking.
                + Does not support Compression.
                + MMAPV1 uses B-trees to store indexes.
                + Storage Engine works on Multiple reader single writer lock. A user cannot have two write calls to be                + processed in parallel on the same collection.
                + Fast for reads and slow for writes.
        + WiredTiger
            - Default Storage Engine starts from MongoDB 3.2 version.Created by BerkelyDB and later taken by Oracle's NoSQL DB.
            - Wired Tiger supports
                + Document-level concurrency model
                + Compression
                + Encryption at Rest for MongoDB Enterprise Edition
                + Durability with and without journal
                + B-trees by default but also supports LSM trees
                + No Locking Algorithms like Hash Pointer.
            - WiredTiger yields 7x-10x better write operations and 80% of the file system compression than MMAP.
            - Compression:
                + Compresses indexes and collections: Compression works on the principle that identifies repeating values or values like patterns that can be stored once in compressed form thereby reducing the total amount of space. Larger units of data tend to compress more effectively as there are possibilities for repeating values and patterns.
                + Available compressors are - snappy, zlib, and none.
                    - Snappy:  Default Compression and low overhead. Efficiently use resources
                    - Zlib: It is similar to gzip and provides 10X better compression than snappy compression but cost more on CPU.
                    - None: When no compression is needed.
                + There are two compression options for indexes:
                    + No compression
                    + Prefix Compression: Effective for some data sets whose values are duplicating. e.g., Country
            - Concurrency:
                + When we are updating an existing record, the update will not happen in original record immediately. It uses multiple version of records. First, make a copy of existing records and update another copy in temporary cache. Threads reading from storage engine will be able to see a different version of data that are committed before read started.
            - Limitations of WiredTiger:
                - WiredTiger is not available on Solaris platform.
                - While updating a bigger document with only a single element, WiredTiger re-writes the whole document, which makes the processes slower.
        + In-Memory Storage Engine
            - Instead of storing documents on disk, the engine uses in-memory for more predictable data latencies.
            - Storage engine uses 50% of physical RAM minus 1 GB as default.
            - Limitation of InMemory Storage Engine:
                + Storage engine does not persist data after a process shutdown.
                + In-memory storage engine requires all its data. When the dataset is huge, then the in-memory engine is not a good option.

