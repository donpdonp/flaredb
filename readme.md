## idfxdb
idfxdb manages a configurable set of indexes. It aims to be an efficient
system to read/write large sets of data objects using a minimal amount of RAM. 

The indexes are seperate and secondary to the data objects in the datastore. The
data objects can be stored in any sort of primary key/value store. For example,
json files on a filesystem. The indexes can be destroyed and rebuilt
using only the data and the index definitions. 

The indexes are kept in lmdb which is an ordered (btree), atomic, mmap-backed
key/value store. Only the pages of the btree needed to retrieve the
index keys for a given query are needed in memory. 

Indexes are defined with a schema that use fields on the data object to build
an index key. The index value is the primary key of the data object. 

*) The only values that can be queried are values that are part of an index 
*) The answer to a query is a set of primary keys. 

Using the primary keys, the data objects can be loaded using another 
mechanism such as the filesystem. 
One implication of this is the database process is not trying to allocate
ram to hold data objects.

Note that idfxdb needs access to
the data object on save, in order to gather the fields needed to build 
the indexes defined for that data object.

