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

