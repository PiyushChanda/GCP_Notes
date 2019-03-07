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

