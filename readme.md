## idfxdb
idfxdb manages a configurable set of indexes used to query a set of data objects. 
It aims to be an efficient system to read/write large sets of data objects using 
a minimal amount of ram/small virtual server. 

A data object is a set of fields that describe the object (such as a javascript object, 
a python dictionary, a ruby hash, etc.) idfxdb expects objects to have two properties, 
an 'id' field containing the primary key in string form (usually a uuid), and a 'type' 
field which groups objects together 
(such as 'user' or 'location').

An index copies fields on the data object to build
an index key. The index value is the primary key of the data object. 
The index key/values are kept in lmdb which is an ordered (btree), atomic, mmap-backed
key/value store. 
Only the pages of the btree needed to retrieve the
index keys for a given query are needed in memory. This keeps memory
usage very low as the database does not try to allocate space to 
hold the data objects themselves.


The index schema defines which indexes exist for which kinds of objects.

* The only values that can be queried are values that are part of an index 
* The answer to a query is a set of primary keys.

The indexes are seperate and secondary to the data objects on disk. 
The indexes can be destroyed and rebuilt using only the data object
and the index definitions. idfxdb will build missing indexes at startup.





