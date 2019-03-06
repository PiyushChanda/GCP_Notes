Is it a good idea to access the cloud sql using cloud proxy?

Realtional databases deal with super structured data, with numerous constraints.
ACID properties are implemented for OLTP processing
No good for OLAP, too slow and too many checks for BI/Analytics/Warehousing, tend to deal with large dataset.

Larger datasets cannot be efficiently handled by relational database.

OLAP applications do not require very strict write consistency. The ACID properties implemented by the Cloud SQL are an overkill for the OLAP Application

Relational Data and Relational Data Model - Tables and relations. Schema defines the column names and datatypes. Doesn't have heirarchical relationship. Not much missing values, as in case of high number of missing values, the columnar database works better.

Cloud Spanner is Google Proprietory, and gives horizontal scaling, hence higher number of instances, replication.
Cloud SQL is open source.

Cloud SQL Offers two implementations, one with MySQL and another with a beta version of Postgre sql.

MySQL - fast and ususal, secure
Postgre - Beta - very complex queries

It is not serverless, as we need to spcifically instantiate cloud sql, always for apps with transaction support, we have to specify region while creating an instance.

First vs. Second gen instance.
Second Generation instances allow proxy support, no need to whitelist ip address or configure ssl.
Higher availability support.
Maintenance won't take down the server, so it is pretty intuitive to use the 2nd generation instance over the 1st generation.

High availability configuration - have a failover replica, in a different region. The original instance is called the master.

all changes made to the master, whether to user or the system tables, are replicated to the fail-over replica using semi-synchronous replication.

# Cloud Proxy

Provides secure access to Cloud SQL 2nd generation instance without having to whitelist ip or configure ssl.

The proxy automatically encrypts data to and from the database and SSL certificates are use to verify client and server identities

Easier Connection management - The proxy handles the authentication with google cloud sql, removing the need to provide static id addresses.

Cloud SQL Architechture.

The cloud sql works by having a local client, called the proxy client, running on the client machine.
The client application only communicates with the proxy. Due to which there is additional deal of security.
Client App -- <Odbc/Jdbc or any standard database procol> -- Proxy Client |--<secure tcp tunnel/encrypted connection>--| Proxy Server -- Cloud sql |

When we start the proxy on the client side, you need to tell the proxy to which server side proxy/cloud sql instance it should connect to
Also where it will listen for data coming from the cloud sql application to be sent to cloud sql.

This is important because the data will not be sent through standard TCP port.

Also we need to tell where to find the credentials to authentical the client sql application to the Coud SQL Instance.

Q - What kind of hard disk would you use for your cloud sql instance if the database is used for production deployment?
A - SSD
Cloud SQL Instance, and create a database within it.
Cloud SQL requires and instance to be set up, that means it is not serverless.

2nd gen instance supports 5.7 and 5.6 both. It runs using InnoDB Execution Engine On Google Cloud.SSD is recommended for production. Also having more CPU/RAM will have more throughput.

For production we can enable automatic storage increases, all increase are permanent.
If the instance is required to serve high traffic, it is recommended to setup auto backup and high availability.

Enable -> Binary Login - Point in time recovery, to restore a data from a time in past, and replication.
We moght also need a failover replica
We can set up the authorized ip addresses that need to connect to the database.
We can aslo set cloud sql flags to customize the database, the list of flags comes in a drop down menu.

Access Control - To add users other than the present root user
Databases - Lists the databases
We can also create a database, or tables within the database using the web console.

Q - How does cloud SQL allow access via the cloud shell command line?
A - By Setting up a temporary whitelist for the ip of the cloud shell, this is done temporarily as the cloud shell is ephemeral
and it has to do it again.

gcloud beta sql connect test-sql --user=root
It whitelists your ip temporarily, and asks for your password before the connections.
Redo the Labs from the Lectures

So we saw how we can create tables using the mysql file residing in your cloud storage, and also load the csv files from the Cloud Storage as well.

Q - What is Horizontal Scaling, and what GCP Platform offers it?
A - Horizontal scaling involves the adding of additional instance to increase the capacity of the software, without increase the available resources to the single machine. Opposite of this is vertical scaling where we increase the resources in a particular machine, which was the case in a traditional database - Monolithic Computing in which we would need to add memory/disk space to a single computer. Horizontal Scaling makes use of distributed computing. GCP adds server capacity/instance automatically when required.

## Cloud Spanner
Is another transaction storage option, google proprietory, horizontal scaling, add more node instances to get increased performance.

We use this to get
1. High availability
2. High Transaction Support ACID++, and strong consistency
3. Transactional reads and writes, especially writes.

Don't use if you don't need the transactional support and if your data cannot be represented in a relational format/not well structured/we want to use open source. If we don't require the strongest write consistency.

It contains tables as usual, it looks relational - rows, columns, strongly typed schemas.
But ...

Okay so suppose we have two tables
Student
###### S_ID, S_Name
1, A
2, B
3, C

and a Transcript
###### S_ID, C_ID, C_Name, Grade
1, 1, Aa, A+
2, 2, Bb, S+
3, 3, Cc, B-

So the data is interleved, i.e. they are stored next to one another. Let us suppose most of our queries require the data base to fetch the transcript of the student. So we assign a parent, here Student is a parent, and Transcript which is the child. So the primary keys are taken and the data is interleved. Every row from the student table will be followed by all of the rows of the child table which have the same primary key value. We can have upto 7 levels nested Parent Child Relationship. This causes access to be very fast. If we want all the data for a particular id of student data, all of that data are going to apprear together in the disk. So we made the Transcript table a child of the Student table. Data locality was enforced between the two seemingly separate tables. Must have a primary key. We are causing the parent primary key to prefix the primary key of the child. Rows are sorted on the basis of their asc order. child rows are inserted in between the parent rows. So all the values related to parent primary key can be easliy picked off, by using a scan. It is almost HBase like.

###### S_ID, S_Name
1, A
###### S_ID, C_ID, C_Name, Grade
1, 1, Aa, A+
###### S_ID, S_Name
2, B
###### S_ID, C_ID, C_Name, Grade
2, 2, Bb, S+
###### S_ID, S_Name
3, C
###### S_ID, C_ID, C_Name, Grade
3, 3, Cc, B-

So, the data is physically stored in this interleved format similar to HBase, which enables fast scanning of sequential data.

Q - Cloud Spanner has tremendous support for transaction processing. This makes even a single read call very slow, true or false?
A - No this statement is false. There are two transaction modes, read only txns, gets priority, and executes very quickly. One off read call - This gives very light txn support, so the queries execute very quickly.
ACID ++ -> Transaction will serialize in the order of their timestamps and those timestamps ar linked to the physical time which we can measure in a watch.

Vulnerable to Hotspotting - Do not use monotonically increasing column as primary key, if you use it so then all the data will be stored in a single location somewhere. So it won't be able to use the power of distributed computing, and it wil kill your performance. Use hash keys in place of monotonically ordered keys. 
- Writes will be on the same shard on the same location in some dist file system. Kill parallelism in cloud snapper. We need to make sure the reads and writes are distributed all over the range of key-values, we need to be careful while choosing the primary key. For ordered columns - use auto hash.

So we change S_ID to some random alpha numeric data, that has very less chance of being monotonically increasing or decreasing.

So the Cloud Spanner under the hood, should modify the interleved representation, and distribute the data over multiple locations, in the form of shards. These could lie in entirely diff storage unit. It will try to modify the internal representaion of the data, it will try to move the data around so that the reads and writes are evenly distributed.

Splits - Logical groupings of data that can be moved independently of one another are called splits.

for Cloud Spanner splits are imp. They are added to distribute high read-write data, and to break up hotspots. This is done on the basis of observing query or read/write patterns in the DB.

So we also have splits that correspond to each value of the student id.

Unlike HBase, tables can also have secondary indices.
 - Might cause some data to be stored twice.

Like HBase, we have a fast sequential scanning of data, and tables must have primary keys.

We might want to create an index on some column that is already interleved with a primary key data. cloud spanner will create another copy that will serve as an index of the original interleved data, but this time on a different column.

We can force a query to use a specific index using index directives - Force Query to use a specific index.

We could force a column to be to be copied to a seconday index using a storing clause.

So if we want the grades on the basis of courses, then we would require a parent-child relationship with the two tables.
Course - Transcript. We would want to create a secondary index, and the data in these two tables will be interleved.

So we will have another version of the data in the grades table, this time interleved with course table.

So course table is stored twice.

## Datatypes
1. Schema is strongly typed.
2. We have ARRAYs and STRUCTs
3. Structs are not okay in tables, unless a query returns array of arrays in the form of structs
4. Arrays are okay in tables, but array of array are not.
Cloud spanner is blurring the line between rdbms and big data technologies.

## Transaction Support
1. Serializability - Transactions which are interleved will commit in some serializable order
2. Transaction support is stronger than traditional ACID
 - Transaction commit in an order reflected in their commit timestamps.
 - These commit timestamps are realtime, hence you can compare them in your watch
3. The transactions are going to commit in the order of their commit timestamps.

### Transaction Modes
Locking Read/Write - Slow
Read only - Fast
If making one-off reads - "Single Cell Reads" - Fastest, no transaction checks at all.

## Staleness
We can set timestamp bounds on your queries/transactions.
 - Strong - Read latest data, to get the latest data
 - Bounded Staleness - "Read verson no later than <timestamp>...", we are providing the level of staleness that we are okay with
 - Exact Staleness - "Reads data at a particular <timestamp>..." we can use this to read the values with the past/future timestamps.

Q - Recommended no of nodes for Cloud Spanner for Prod?
A - 3 nodes for production environment
Instance Id is permanent and will be used to identify the spanner instance.

We can create an instance/database/table easily using the console. During creation of tables, we can specify the inteleving - Parent Child realtionship between the tables. The query to do so is written in spanner sql, which is slightly different from mysql. The cloud console offers insights on how to create the tables/interleving/primary keys etc.



