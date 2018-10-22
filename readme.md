## IxFxDb
Idfxdb manages a configurable set of indexes. It aims to be an efficient
system to read/write large data sets using a minimal amount of RAM. 

The indexes are seperate and secondary to the data in the datastore. The
data can be stored in any sort of primary key/value store. For example,
json files on a filesystem. The indexes can be destroyed and rebuilt
using only the data and the index definitions. 

The indexes are kept in lmdb which is an ordered (btree), MVCC, mmap-backed
key/value store. Only the pages of the btree needed to retrieve the
index keys for a given query are needed in memory. 

Indexes are defined with a schema that use fields on the data object to build
an index key. The index value is the primary key of the data object. 

