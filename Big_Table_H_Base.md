# Big Table Intro
Q - Why is it easier to add table in BigTable than in Cloud Spanner?
A - Big Table, columnar, all we need to do is insert new rows in the undelying data. In cloud spanner we need to do an alter table, there will be change in schema, there will be database writes, writes require txn support. And also, the interleved underlying data, gives rise to a whole bunch of difficulty when we try to change the column structure.
Big Table - Fast Sequential Scanning of columnar data, Low Latency

- Good for sparse data
- Stores sequential key values in sorted order
- If the read/write are not evenly distributed, the performance can take a bad hit
- Managed version of HBase
- closer link than that of Hive and BigQuery, as the underlying data storage in Hive and BigQuery are completely different.
Advantages
- Scalability
- Low Ops Burden/Admin Burden
- Resize cluster without downtime
- has the ability to support many more column families before the performance drops ~ 1000k

Properties, also properties of HBASE
- Columnar storage, has 3 columns, effectively four
- Has denormalized storage, which is different from the Rdbms
- Focuses on CRUD operations, create, read, update, delete, Simpler set of DDL opeations that are supported
- ACID at the row level, the transaction support is almost non-existent

Columnar Data Store

Id, to, type, content
---------------------
1, A, B, C
2, D, E, F
3, G, H, I

Id column is common

Id, Column, Value
-------------------
1, to, A
1, type, B
1, content, C
2, to, D
2, type, E
2, content, F
3, to, G
3, type, H
3, content,I

Conclusion
1. One row in rdbms corresponds to 3 rows/multiple rows in The columnar database
2. Not normalized

Disadvantage
1. Denormalized Data

Advantages
1. Sparse Data saves a lot of space, because for every null value, we get one row less in the Columnar form.
2. Dynamic Attributes, adding a column dynamincally won't change the underlying schema. And adding one colunm corresponds to adding one row in the underlying representation.

In an rdbms, carrying out an alter table operation would have significant penalty.

# Columnar store
Q - Normalization of data is great for traditional database theory, but has drawbacks on distributed world. What are some?
A - Normalization - Save Space - Monolithic Nature, so bottleneck on size/ram of a single monolithic machine. But in a distributed world, bottleneck is bandwidth, network access, node access. If we store data in multiple nodes, we access network 3 times, so bad performance is the result even if we saved a few space. So we need to keep all the related information close together, as disk seeks are more expensive than storage.

Sparse Data -
For a null value, there won't be any row corresponding to it in the Columnar database.

In traditional database, we work with datasets of low datasize, so the space lost while storing nulls is negligible, when compared to the data that columnar databases work with. This data is in the range of petabytes, so we are not okay with the data lost with null values.

Dynamically add attribues to the tables. We can easily load a new column as rows in columnar data.

These are schematic diagrams, and are a good guide on how the database of columnar type work. Don't try to reverse engineer the location/actual physical location on which the data is stored.

Minimising redundancy - Rdms, Solution - 3rd Normal Form, widely used, We have a more complex data model.

Q - BigTable supports equijoins, but not indices/ constraints. True or False?
A - False

Equijoins - Joining on an equalto condtion

Normalisation - Optmize Storage
But now, storage is very cheap. We have a large number of generic machines each with a large number of attached storage.
Disk seeks to data/servers is expensive in a distributed computing world.
To get the Person/Eployee data, we might need to hit 3 tables which may recide in 3 different locations/servers accross the distrbuted computing system.

In a denormalized environment, we can have Employee, Subordinate and Address Information as shown below.

Emp Number,Name,Email,Subordinate<Array-this violates the first normal form>,Address<Struct - This is a little complex data structure than an array>
  
So basically we have stored all of the related information together in a single table, even though it is denormalized.

All info in only one reach operation. All logically/physically stored near to one another.
## Crud
HBase and BigTable only support CRUD Operations.
Create
Read
Update
Delete

Not Supported

No Group By - Accross rows/tables
No Sorted - Accross rows/tables
No joins - Accross rows/tables

They involve some sort of comparison accross different rows of the same data. These are not supported in HBase/Big-Table

so they can't perform comparisons with groups of rows.
so they are no-sql technologies, because they don't effectively support sql. Row is a basic unit, and it is their way of viewing the world.

BigTable doesn't support indexes on columns other than the row key, and doesnot support constraints.

In case of BigTabble, all the data must be self contained in one row.
## Acid
Hbase only support ACID at a row level.

Updates to a single row in HBase are atomic, that is, all the columns in a row are updated or none will be.

Updates to multiple rows are not atomic, even if the update in on the same column, but on the multiple rows.

The world view of a columnar data store is restricted to the groups of data with same row id, once we cross the row, all bets are off.

Difference betw Traditional rdbms and Columnar Data Storage
1. data is arranged in rows and columns, data is stored in columns only
2. sql compliant, no-sql
3. normalization - reduces redundancy, denormalize just so that the data can be accomodated in a particular location
4. complex queries can be done, cannot be done in a no-sql database
5. ACID, ACID on a row level, multirow level changes are not ACID compliant

# Column Families
Q - How does the choice of row key affect the physical storage in Big table or in HBase?
A - Row keys are soted in lexicographical order, and data are stored in that order, this has implications on the performance of the system.

In an rdbms, to uniqely identify a data item, we would require row, column and the table where the data resides.

Also, in the modern database implementations, Database maintain multiple versions of the same data on the basis of timstamps.
So the dimensionality of a single atomic value in an rdbms increases to 4.
A type of versioning based on timestamps is done by most modern databases.This 4 dimensional view of a particular value is used by HBase as well.

4 Dimensional Data Model of HBase.

Row Key, Column Family - the table name is encoded in the column family field, and this is how Hbase is able to create multiple tables corresponding to a single database in one columnar store, column-name -> Individual column name from a typical rdbms schema, Last is the timstamp.

Spanner -> Had Staleness bounds,latest/no later than/no older than -> Supports different versions, based on timestamp, 
and HBase Does the same.
So we have different versions based on different timestamp on the same data item.

So we have something like this.
--------|---------------------------|-------------
Row Key |       Column Family       |Column Family
--------|---------------------------|-------------
        | Col1 | Col1 | Col1 | Col1 | Col1 | Col1 
        |---------------------------|-------------
        | Value
        |------
            |--Timestamp1
            |---Timestamp2
            |---Timestamp3
            
If we don't specify any timestamp the latest data is taken from the columnar data store.
We can access a value using rowkey, column family, column name and timestamp. We can choose to view stale records if we want to.

Row Key - Uniquely identifies a row, can containe primitive value, structures or arrays, compuond datatypes, internally it is stored as byte array, stored sorted in ascending order. And the groups are stored in lexicographically sorted order of it's row key, this might have an impact over the performance of the HBase.

Column Family - All rows have same set of column families, each column family is going to be stored in a separate data file.
Kind of similar to a table. Column family must be set up front at the time of schema definition, different from column that can be specified dynamically on the fly, and also it is ok to have different columns for each row, kind of like a document database

Columns - Fields in rdbms, units in column families, new columns can be added on the fly unlike column family. To uniqely identify a column, you need to specify which column family it belongs to.

ColumnFamily:ColumnName - kind of like namespace.

Timestamps - HBase's ability to store different versions for a data in a specific column. You can specify the timestamp for which you want the data to access, or it will simply retrieve the most up to date version of the data.

# BigTable Performance
Hbase is atomic at what level?
1. Column Family
2. Column
3. Row
4. None

Avoid BigTable when,
1. We require transaction support, it is better to use Spanner/Cloud SQL
2. Donot use BigTable if your data size is less than one TB. Can't parallelize. BigTable won't be able to use under the hood optimizations like sharding and distributed storage.
3. Analytics/Data Warehouseing/Business Intelligence - BigQuery is a beter choise as 1. It has a sql like interface, 2. supports complex queries 3. Big Query is performant than hive, can be used for real time operations too.
4. Don't use Big Table in case of highly hierarchical data, that is more in the realm of Datastore/Mongo DB/Couch Db
5. Don't use for immutable files like BLOBS/media content.

User for
1. Very fast scanning for low latency high throughput application, scanning sequential row ids.
2. For non structured key-value data, because in this case, the rdbms won't work. If there is a single key, think of BigTable, if there are multiple keys, think of a document driven database like datastore
3. When each dataitem < 10 MB and Total Data > 1 TB
4. Where write is insignificant/infrequent but we care about fast scans, if we are using timeseries data
5. We can also use timestamps as the value of the row key.

Like Cloud Spanner
1. Data is stored in Lex order of keys
2. Data is sharded based on those key values

So performance will be poor if,
1. Writes/Reads are concentrated on some ranges of the key values.
2. for instance key-values are sequential

We can hash the key-values in cloud spanner and non-sequential keys.

Avoid Hotspots
1. Field Promotion - Structured Key - Arranged in reverse url order. So that the names become similar in the front, and different in the end. So related data items are stored nearby. If the sequential scans are based on some value of the key-prefix, then all the related data items are picked up at one go. Pretty standard way.
2. Salting - Hashing the key value, like Spanner

Warming the cache
1. Big Table or HBase will try to improve the performace over time. It observes the read and write patterns of the data, and re-distributes them so that shards are evenly hit. This is more prominent in BigTable than HBase, roughly store equal amount of data in every shard. Testing of performace should be done over a long time, hours instead of minutes to allow HBase or BigTable to move data around, so as to eliminate hotspots.
2. Otherwise we would get misleadingly poor performance.

SSD or HHD - Use SSD, if you don't have a very limiting budget
SSD are 20x faster than hard disks, for individual row reads, it is less fast when we consider batch reads/sequential scans
SSDs are more predictable in terms of their throughput, and this gives bigtable to correctly optmise the data distrbution. If like HDD, the performance is variable, that could through the calculations of BigTable off for a spin.

So HDD is better when,
1. Data size is greater than 10 TB
2. And you mostly run all batch queries

The more random access you perform on your big table, the better it is to go for an SSD
** If all of your queries is of random access nature then BigTable is not even the tool for you, you should go for the document database.

Reasons for its poor performace,
1. Schema Design - Sequential Keys, causing reads/writes in the same physical location
2. Inappropriate workloads - 
  Data Size too small < 300 GB, it should be > 1 TB
3. Queries run in short bursts, when it actually takes hours to tune performance internally.
4. Cluster too small
5. Cluster Just been fired up/scaled up - So Big Table would require some time to tune it's performance based on newly available resources
6. HDD or SSDs
7. Development Vs Production - Performance particularly great in Production than in development

Schema Design - 
1. You have only one index - the row key, choose it wisely. You don't have the luxury to have multiple indexes likes of which are present in Datastore and Cloud Spanner
2. Row keys are sorted lexicographically
3. Acid on row level, Multi row changes are not Atomic
4. Related Entities are present in Adjacent rows, realy fast sequential scanning

Row Key choices
1. Reverse domain names
2. Strings are better too because they hash evenly
3. Timestamps but only as suffixes, as in prefix they become sequentially increasing keys causing hotspotting

Row Key to avoid
1. Domain names - common portions comes at the end of the row key, and so the adjacent values won't be logically related
2. sequential numeric values
3. Timestamps alone/prefixed by Timestamp
4. Fields that are to be changed repeatedly are not suitable as a row key, Ideally they should be immutable

Limitation -
Row Key: 4KB per key value
Column Families per table should be less than 100
Individual Column values should not exceed about 10 MB in size
Total Row Size - should not exceed about 100 MB

Use the GCP Cloud - Lecture 53

Q - What are the two operations that are very low latency in BigTable?
A - Very fast lookup operations on row keys. Row Keys are used as index of the table, with very fast scan operations. Range of row-keys can be looked up very quickly.

WE need to create a Big Table instance First
Prod - Available, min 3 nodes, cannot be upgraded
Deve - For playing around, not highly available, can be upgraded
We use hbase shell to work with BigTable

 hbase> 
 
 gcloud beta bigtable instances list
 
 ./quickstart.sh
 Picks up one bigtable instance from your project.
 You have to be authenticated and logged in with default project having BigTable instance
 
  hbase> list -- to get the tables in BigTable
  hbase> create 'students', 'personal' -- to create a students table with person column family
  hbase> -- all columns are logically grouped into column families
  hbase> -- each table should have atleast one column family
  hbase> list -- should show the students table
  hbase> put 'students', '12345', 'personal:name', 'John' -- This will insert into students table, with a row key value of 12345 and the value of the 'name' column of 'personal' column family is 'John'
  hbase> -- row key is important for every row, this enables very fast scan of rows over a range of values of row-keys
  hbase> -- columnFamily:columnName
  hbase> put 'student', '12345', 'personal:State', 'Emily' -- row key should be unique
  hbase> scan students -- prints the table
  hbase> -- row keys are printed twice because there are two columns worth of information
  hbase> -- for every row, we can see the name of the column family and name of the column. They are assossiated with timestamps which forms the performing information
  hbase> disable 'students'
  hbase> drop 'students' -- deletes the table
  
