Q - What data structure has contstant lookup, irrespective of the data structure?
A - Hash Table

# Datastore
For document oriented storage in a no-sql database/mongo db/couch db
Document data like html/xml are stored
key-value structure, but unlike Hbase, there are large number of differring keys and those keys are heirarchically realted to each other.
Whole bunch of indexing is done on the key-values

Use Case -
Not for OLTP or OLAP, but when we need crezy fast lookup over key pairs.
This would scale infinitely as the size of the data increases.

Indices are hash based which makes this possible
1. Txn support is better than other NoSQL Products, not as cloudSQL/Spanner. As it needs to make sure that the indices are well-maintained.
2. The query execution time is dependent on the size of the result not the size of the underlying dataset
3. Perfect for needles in the heystack applications, not like HBase or BigTable where we perform a sequential scan on bunch of row key values.

Traditional
 Atomic support. Indices for fast lookup. NOT all queries use indices.

Datastore
 Atomic support, for the need to make all those internal indices consistent with each other. All queries use indices.
