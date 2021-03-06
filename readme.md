## flaredb
flaredb manages a configurable set of indexes used to query a set of data objects. 
It aims to be an efficient system to read/write large sets of data using 
a minimal amount of ram/small virtual server. 

A data object is a set of fields that describe the object (such as a javascript object, 
a python dictionary, a ruby hash, etc.) idfxdb expects objects to have two properties, 
an 'id' field containing the primary key in string form (usually a uuid), and a 'type' 
field which groups objects together 
(such as 'user' or 'location').

An index copies fields on the data object to build
an index key. The index value is the 'id' field of the data object. 
The index key/values are kept in lmdb which is an ordered (btree), atomic, mmap-backed
key/value store. 
Only the pages of the btree needed to retrieve the
index keys for a given query are needed in memory. 

The index schema defines which indexes exist for which kinds of objects.

* The only values that can be queried are values that are part of an index 
* The answer to a query is a set of primary keys.

The indexes are seperate and secondary to the data objects on disk. 
The indexes can be destroyed and rebuilt using only the data object
and the index definitions. idfxdb will build missing indexes at startup.

## Usage 
Index properties are the &lt;name&gt;, the fields that make up the key [&lt;field1&gt;, ...], and config options.
```javascript
  let schema = {
    'user': {
      indexes: [
        ['username', ['username'], { unique: true, lowercase: true }],
        ['email', ['email'], { unique: true, lowercase: true }]
      ]
    },
    'location': {
      indexes: [
        ['user_id_date', ['user_id', 'date'], {}]
      ]
```

If the primary key is known, thats indexed for free by the filesystem
```javascript
  let user = idfx.loadFile('57c0e740-d636-11e8-97d2-3358493f968d')
```

Use the index named 'username' for type 'user' to find the specific username of 'somebob'.
```javascript
  let index = schema['user'].indexes[0]
  let key = 'somebob'
  let user_id = idfx.get('user', index, key)
  let user = idfx.loadFile(user_id)
```

Use the index named 'user_id_date' for type 'location' to find a range of values for a user_id
between the date strings for 2018-08-01 and now.

Note in the range query both the keys and values are returned. In a simple retrieve query the
key is known beforehand, but in a range query the specific keys are part of the answer and 
can sometimes contain information to satisfy the query without loading the data object.

```javascript
  let index = schema['location'].indexes[0]
  let start = new Date("2018-08-01").toISOString()
  let stop = new Date().toISOString()
  let idx_kvs = this.getIdxBetween('location', 'user_id_date', 
                                        [user_id, start],
                                        [user_id, stop])
  let locations = Object.keys(idx_kvs).map(ikey => idfx.loadFile(idx_kvs[ikey]))

```

## Architecture choices

Langs
  * go
  * zig

RPC
  * library/in-process
  * nng/mangos/Scalability Protocol

Index store
  * lmdb

Object serialization
  * json
  * protobuf

Object store
  * filesystem

## Status
This code is working inside the icecondor project's node server. It will be
extracted into this repo and improved.

## Roadmap
* data object read/write configurable functions (currently fixed to json/filesystem)
* in-project library or RPC mechanism - http, zeromq
* adapt to node's levelup? not sure it supports the required attributes that lmdb has
* binary key values+length instead of utf8 string+seperator


