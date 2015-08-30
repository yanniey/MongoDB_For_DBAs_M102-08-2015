## Homework 4.5

Answer: `R`

```
// Connect to Secondary database

Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/Homework4_1$ mongo --port 27003 --shell replication.js


M102P:SECONDARY> use local
switched to db local
M102P:SECONDARY> show dbs
local        0.094GB
replication  0.031GB
M102P:SECONDARY> show collections
me
oplog.rs
replset.minvalid
startup_log
system.indexes
system.replset

// check to make sure that I'm in the right database
M102P:SECONDARY> db.oplog.rs.find()
{ "ts" : Timestamp(1440912224, 1), "h" : NumberLong("-5319659329664534532"), "v" : 2, "op" : "n", "ns" : "", "o" : { "msg" : "Reconfig set", "version" : 3 } }
{ "ts" : Timestamp(1440912966, 1), "h" : NumberLong("-5319659329664534532"), "v" : 2, "op" : "n", "ns" : "", "o" : { "msg" : "Reconfig set", "version" : 4 } }


M102P:SECONDARY> db.oplog.rs.stats()
{
	"ns" : "local.oplog.rs",
	"count" : 2,
	"size" : 200,
	"avgObjSize" : 100,
	"numExtents" : 1,
	"storageSize" : 52428800,
	"lastExtentSize" : 52428800,
	"paddingFactor" : 1,
	"paddingFactorNote" : "paddingFactor is unused and unmaintained in 3.0. It remains hard coded to 1.0 for compatibility only.",
	"userFlags" : 1,
	"capped" : true,
	"max" : NumberLong("9223372036854775807"),
	"maxSize" : 52428800,
	"nindexes" : 0,
	"totalIndexSize" : 0,
	"indexSizes" : {

	},
	"ok" : 1
}

// Test

M102P:SECONDARY> db.oplog.rs.find( { } ).sort( { $natural : 1 } ).limit( 1 ).next( ).o.msg[0]
R
```

