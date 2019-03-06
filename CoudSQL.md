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

