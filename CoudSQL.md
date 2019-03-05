Is it a good idea to access the cloud sql using cloud proxy?

Realtional databases deal with super structured data, with numerous constraints.
ACID properties are implemented for OLTP processing
No good for OLAP, too slow and too many checks for BI/Analytics/Warehousing, tend to deal with large dataset.

Larger datasets cannot be efficiently handled by relational database.

OLAP applications do not require very strict write consistency. The ACID properties implemented by the Cloud SQL are an overkill for the OLAP Application

