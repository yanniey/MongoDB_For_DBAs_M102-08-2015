# M102: MongoDB for DBAs

August 2015 with MongoD Version 3.0.3


---
## Week7: Security

Two ways to authenticate:

+ Run `mongos` and `mongod` with `--auth`
+ Use `--keyFile`

```
mkdir data
mongod --path data --auth
```
then in another shell:

```
mongo localhost/test
```

Use `admin` database to manage accounts, 1) create a user, 2) authenticate it 

```
use admin
var me = {user: "anyi", pwd:"asdf",roles:["userAdminAnyDatabase"]}
db.createUser(me)
```

Exit mongo, then log in again as the newly created user. This admin user can access the `admin` db, but cannot read or write the other dbs:

```
mongo localhost/admin -u anyi -p asdf
```

Create another user that can read and writer other dbs:

```
var a = {user: "anyi_rw", roles:"readWriteAnyDatabase", pwd:"abc"}
db.createUser(a)
```

Create a user that can read and write only one (the current) database

```
var b = {user: "anyi_rw_only", pwd:"asdf",roles:["readWrite"]}
db.crteaUser(b)
```


---
## Week 6: Scalability (Sharding/Partitioning)

1. Initiate shards, replica sets, config servers/dbs, 
2. Initiate on 1 replSet server, then add the other 2 servers to the replSet 
3. Add shard to cluster
4. Check to make sure that shards are added to cluster by using 

```
use config
db.shards.find()

```

range-based("chunk", ~100MB each) partitioning

the more shards you have, the more replica sets you want to make sure that data is preserved when a server goes down.


#### Operations:

+ "splits": inexpensive, divide a shard into multiple ones
+ "migration": move data from one shard to another, expensive, maintains balance

#### Config server:
It contains metadata and tells you which server the data is on.

#### Mongos:
The clients talk to `mongos`, and then `mongos` will talk to everyone on behalf of the client.


#### Demo: Cluster Setup

How to set up :

+ 4 shards 
+ with replication factor of 3 (each shard has 3 replica set servers) 
+ with 4 mongos processes and
+ 3 config servers

```
# running on a single dev machine for demo

mkdir a0
mkdir a1
mkdir a2
mkdir b0
mkdir b1
mkdir b2
mkdir c0
mkdir c1
mkdir c2
mkdir d0
mkdir d1
mkdir d2

mkdir cfg0
mkdir cfg1
mkdir cfg2

# config servers

mongod --configsvr --dbpath cfg0 --port 26050 --fork --logpath log.cfg0 --logappend
mongod --configsvr --dbpath cfg1 --port 26051 --fork --logpath log.cfg1 --logappend
mongod --configsvr --dbpath cfg2 --port 26052 --fork --logpath log.cfg2 --logappend

# "shard servers"(mongod data servers)
# note: don't use smallfiles or such a small oplogSize in production; these are here as we are running many on one machine

mongod --shardsvr --replSet a --dbpath a0 --logpath log.a0 --port 27000 --fork --logappend --smallfiles --oplogSize 50
mongod --shardsvr --replSet a --dbpath a1 --logpath log.a1 --port 27001 --fork --logappend --smallfiles --oplogSize 50
mongod --shardsvr --replSet a --dbpath a2 --logpath log.a2 --port 27002 --fork --logappend --smallfiles --oplogSize 50
mongod --shardsvr --replSet b --dbpath b0 --logpath log.b0 --port 27100 --fork --logappend --smallfiles --oplogSize 50
mongod --shardsvr --replSet b --dbpath b1 --logpath log.b1 --port 27101 --fork --logappend --smallfiles --oplogSize 50
mongod --shardsvr --replSet b --dbpath b2 --logpath log.b2 --port 27102 --fork --logappend --smallfiles --oplogSize 50
mongod --shardsvr --replSet c --dbpath c0 --logpath log.c0 --port 27200 --fork --logappend --smallfiles --oplogSize 50
mongod --shardsvr --replSet c --dbpath c1 --logpath log.c1 --port 27201 --fork --logappend --smallfiles --oplogSize 50
mongod --shardsvr --replSet c --dbpath c2 --logpath log.c2 --port 27202 --fork --logappend --smallfiles --oplogSize 50
mongod --shardsvr --replSet d --dbpath d0 --logpath log.d0 --port 27300 --fork --logappend --smallfiles --oplogSize 50
mongod --shardsvr --replSet d --dbpath d1 --logpath log.d1 --port 27301 --fork --logappend --smallfiles --oplogSize 50
mongod --shardsvr --replSet d --dbpath d2 --logpath log.d2 --port 27302 --fork --logappend --smallfiles --oplogSize 50

# mongos processes

mongos --configdb Anyis-MacBook-Pro.local:26050,Anyis-MacBook-Pro.local:26051,Anyis-MacBook-Pro.local:26052 --fork --logappend --logpath log.mongos0

mongos --configdb Anyis-MacBook-Pro.local:26050,Anyis-MacBook-Pro.local:26051,Anyis-MacBook-Pro.local:26052 --fork --logappend --logpath log.mongos1 --port 26061

mongos --configdb Anyis-MacBook-Pro.local:26050,Anyis-MacBook-Pro.local:26051,Anyis-MacBook-Pro.local:26052 --fork --logappend --logpath log.mongos2 --port 26062

mongos --configdb Anyis-MacBook-Pro.local:26050,Anyis-MacBook-Pro.local:26051,Anyis-MacBook-Pro.local:26052 --fork --logappend --logpath log.mongos3 --port 26063

# check and print the last line of each file

echo 
ps -A | grep mongo

echo
tail -n 1 log.cfg0
tail -n 1 log.cfg1
tail -n 1 log.cfg2
echo
tail -n 1 log.a0
tail -n 1 log.a1
tail -n 1 log.a2
tail -n 1 log.b0
tail -n 1 log.b1
tail -n 1 log.b2
tail -n 1 log.c0
tail -n 1 log.c1
tail -n 1 log.c2
tail -n 1 log.d0
tail -n 1 log.d1
tail -n 1 log.d2
echo
tail -n 1 log.mongos0
tail -n 1 log.mongos1
tail -n 1 log.mongos2
tail -n 1 log.mongos3

```

Save this as `run.sh` and run it with `sh run.sh`. The output is in the `output.md` file.


In general, you don't want the clients to talk directly to the shard servers. All communications needs to go through `mongos`.

You can have as many `mongos` processes as you want, but generally more than 1.

## The Config Database

All config servers have exactly the same data.

Note: be sure to connect to `mongos` by either using the default port or specifying the port

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week6$ mongo
MongoDB shell version: 3.0.3
connecting to: test
mongos> use config
switched to db config
mongos> db
config
mongos> show collections
actionlog
changelog
chunks
databases
lockpings
locks
mongos
settings
shards
system.indexes
tags
version
```

## Adding the Intial Shards

+ First, initiate the replica set(initate on 1 server, then add the other 2)
+ add the shard by `sh.addShard()`

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week6$ mongo --port 27000
MongoDB shell version: 3.0.3
connecting to: 127.0.0.1:27000/test

> rs.status()
{
	"info" : "run rs.initiate(...) if not yet done for the set",
	"ok" : 0,
	"errmsg" : "no replset config has been received",
	"code" : 94
}

> rs.initiate()
{
	"info2" : "no configuration explicitly specified -- making one",
	"me" : "Anyis-MacBook-Pro.local:27000",
	"ok" : 1
}
a:OTHER> rs.status()
{
	"set" : "a",
	"date" : ISODate("2015-09-13T19:04:29.508Z"),
	"myState" : 1,
	"members" : [
		{
			"_id" : 0,
			"name" : "Anyis-MacBook-Pro.local:27000",
			"health" : 1,
			"state" : 1,
			"stateStr" : "PRIMARY",
			"uptime" : 2597,
			"optime" : Timestamp(1442171063, 1),
			"optimeDate" : ISODate("2015-09-13T19:04:23Z"),
			"electionTime" : Timestamp(1442171063, 2),
			"electionDate" : ISODate("2015-09-13T19:04:23Z"),
			"configVersion" : 1,
			"self" : true
		}
	],
	"ok" : 1
}

a:PRIMARY> rs.add("Anyis-MacBook-Pro.local:27001")
{ "ok" : 1 }
a:PRIMARY> rs.add("Anyis-MacBook-Pro.local:27002")
{ "ok" : 1 }

a:PRIMARY> rs.status()
{
	"set" : "a",
	"date" : ISODate("2015-09-13T19:05:08.462Z"),
	"myState" : 1,
	"members" : [
		{
			"_id" : 0,
			"name" : "Anyis-MacBook-Pro.local:27000",
			"health" : 1,
			"state" : 1,
			"stateStr" : "PRIMARY",
			"uptime" : 2636,
			"optime" : Timestamp(1442171105, 1),
			"optimeDate" : ISODate("2015-09-13T19:05:05Z"),
			"electionTime" : Timestamp(1442171063, 2),
			"electionDate" : ISODate("2015-09-13T19:04:23Z"),
			"configVersion" : 3,
			"self" : true
		},
		{
			"_id" : 1,
			"name" : "Anyis-MacBook-Pro.local:27001",
			"health" : 1,
			"state" : 2,
			"stateStr" : "SECONDARY",
			"uptime" : 6,
			"optime" : Timestamp(1442171105, 1),
			"optimeDate" : ISODate("2015-09-13T19:05:05Z"),
			"lastHeartbeat" : ISODate("2015-09-13T19:05:07.192Z"),
			"lastHeartbeatRecv" : ISODate("2015-09-13T19:05:07.632Z"),
			"pingMs" : 0,
			"syncingTo" : "Anyis-MacBook-Pro.local:27000",
			"configVersion" : 3
		},
		{
			"_id" : 2,
			"name" : "Anyis-MacBook-Pro.local:27002",
			"health" : 1,
			"state" : 5,
			"stateStr" : "STARTUP2",
			"uptime" : 3,
			"optime" : Timestamp(0, 0),
			"optimeDate" : ISODate("1970-01-01T00:00:00Z"),
			"lastHeartbeat" : ISODate("2015-09-13T19:05:07.192Z"),
			"lastHeartbeatRecv" : ISODate("2015-09-13T19:05:07.197Z"),
			"pingMs" : 0,
			"configVersion" : 3
		}
	],
	"ok" : 1
}


a:PRIMARY> rs.conf()
{
	"_id" : "a",
	"version" : 3,
	"members" : [
		{
			"_id" : 0,
			"host" : "Anyis-MacBook-Pro.local:27000",
			"arbiterOnly" : false,
			"buildIndexes" : true,
			"hidden" : false,
			"priority" : 1,
			"tags" : {

			},
			"slaveDelay" : 0,
			"votes" : 1
		},
		{
			"_id" : 1,
			"host" : "Anyis-MacBook-Pro.local:27001",
			"arbiterOnly" : false,
			"buildIndexes" : true,
			"hidden" : false,
			"priority" : 1,
			"tags" : {

			},
			"slaveDelay" : 0,
			"votes" : 1
		},
		{
			"_id" : 2,
			"host" : "Anyis-MacBook-Pro.local:27002",
			"arbiterOnly" : false,
			"buildIndexes" : true,
			"hidden" : false,
			"priority" : 1,
			"tags" : {

			},
			"slaveDelay" : 0,
			"votes" : 1
		}
	],
	"settings" : {
		"chainingAllowed" : true,
		"heartbeatTimeoutSecs" : 10,
		"getLastErrorModes" : {

		},
		"getLastErrorDefaults" : {
			"w" : 1,
			"wtimeout" : 0
		}
	}
}
```

# adding shard to cluster

```
mongos> sh.addShard("a/Anyis-MacBook-Pro.local:27000")
{ "shardAdded" : "a", "ok" : 1 }
```

```
# check to see if the shard has been added to the cluster

mongos> sh.status()
--- Sharding Status ---
  sharding version: {
	"_id" : 1,
	"minCompatibleVersion" : 5,
	"currentVersion" : 6,
	"clusterId" : ObjectId("55f5be9a47f10f5ff0d0681e")
}
  shards:
	{  "_id" : "a",  "host" : "a/Anyis-MacBook-Pro.local:27000,Anyis-MacBook-Pro.local:27001,Anyis-MacBook-Pro.local:27002" }
  balancer:
	Currently enabled:  yes
	Currently running:  no
	Failed balancer rounds in last 5 attempts:  0
	Migration Results for the last 24 hours:
		No recent migrations
  databases:
	{  "_id" : "admin",  "partitioned" : false,  "primary" : "config" }

```

## Use `setup_script.js` to batch add shard to cluster

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week6$ mongo --shell setup_script.js
MongoDB shell version: 3.0.3
connecting to: test
type "help" for help
setup.js loaded.
list of existing shards before doing anything:
[
	{
		"_id" : "a",
		"host" : "a/Anyis-MacBook-Pro.local:27000,Anyis-MacBook-Pro.local:27001,Anyis-MacBook-Pro.local:27002"
	}
]

You can invoke:
ourAddShard(setname,port)



mongos> ourAddShard("b",27100)
connecting to: localhost:27100/test
undefined
{ "ok" : 1 }
waiting for repl set initiate to complete...
adding shard...
{ "shardAdded" : "b", "ok" : 1 }
done; run sh.status()



mongos> ourAddShard("c",27200)
connecting to: localhost:27200/test
undefined
{ "ok" : 1 }
waiting for repl set initiate to complete...
adding shard...
{ "shardAdded" : "c", "ok" : 1 }
done; run sh.status()

mongos> sh.status()
--- Sharding Status ---
  sharding version: {
	"_id" : 1,
	"minCompatibleVersion" : 5,
	"currentVersion" : 6,
	"clusterId" : ObjectId("55f5be9a47f10f5ff0d0681e")
}
  shards:
	{  "_id" : "a",  "host" : "a/Anyis-MacBook-Pro.local:27000,Anyis-MacBook-Pro.local:27001,Anyis-MacBook-Pro.local:27002" }
	{  "_id" : "b",  "host" : "b/Anyis-MacBook-Pro.local:27100,Anyis-MacBook-Pro.local:27101,Anyis-MacBook-Pro.local:27102" }
	{  "_id" : "c",  "host" : "c/Anyis-MacBook-Pro.local:27200,Anyis-MacBook-Pro.local:27201,Anyis-MacBook-Pro.local:27202" }
  balancer:
	Currently enabled:  yes
	Currently running:  no
	Failed balancer rounds in last 5 attempts:  0
	Migration Results for the last 24 hours:
		No recent migrations
  databases:
	{  "_id" : "admin",  "partitioned" : false,  "primary" : "config" }


mongos> ourAddShard("d",27300)
connecting to: localhost:27300/test
undefined
{ "ok" : 1 }
waiting for repl set initiate to complete...
adding shard...
{ "shardAdded" : "d", "ok" : 1 }
done; run sh.status()
mongos> sh.status()
--- Sharding Status ---
  sharding version: {
	"_id" : 1,
	"minCompatibleVersion" : 5,
	"currentVersion" : 6,
	"clusterId" : ObjectId("55f5be9a47f10f5ff0d0681e")
}
  shards:
	{  "_id" : "a",  "host" : "a/Anyis-MacBook-Pro.local:27000,Anyis-MacBook-Pro.local:27001,Anyis-MacBook-Pro.local:27002" }
	{  "_id" : "b",  "host" : "b/Anyis-MacBook-Pro.local:27100,Anyis-MacBook-Pro.local:27101,Anyis-MacBook-Pro.local:27102" }
	{  "_id" : "c",  "host" : "c/Anyis-MacBook-Pro.local:27200,Anyis-MacBook-Pro.local:27201,Anyis-MacBook-Pro.local:27202" }
	{  "_id" : "d",  "host" : "d/Anyis-MacBook-Pro.local:27300,Anyis-MacBook-Pro.local:27301,Anyis-MacBook-Pro.local:27302" }
  balancer:
	Currently enabled:  yes
	Currently running:  no
	Failed balancer rounds in last 5 attempts:  0
	Migration Results for the last 24 hours:
		No recent migrations
  databases:
	{  "_id" : "admin",  "partitioned" : false,  "primary" : "config" }

```

Check the config server to make sure that shards are added to the cluster

```
mongos> use config
switched to db config
mongos> show collections
actionlog
changelog
chunks
databases
lockpings
locks
mongos
settings
shards
system.indexes
tags
version
mongos> db.shards.find()
{ "_id" : "a", "host" : "a/Anyis-MacBook-Pro.local:27000,Anyis-MacBook-Pro.local:27001,Anyis-MacBook-Pro.local:27002" }
{ "_id" : "b", "host" : "b/Anyis-MacBook-Pro.local:27100,Anyis-MacBook-Pro.local:27101,Anyis-MacBook-Pro.local:27102" }
{ "_id" : "c", "host" : "c/Anyis-MacBook-Pro.local:27200,Anyis-MacBook-Pro.local:27201,Anyis-MacBook-Pro.local:27202" }
{ "_id" : "d", "host" : "d/Anyis-MacBook-Pro.local:27300,Anyis-MacBook-Pro.local:27301,Anyis-MacBook-Pro.local:27302" }
```

## Enable sharding on the DB

```
mongos> db
test
mongos> sh.enableSharding("test")
{ "ok" : 1 }
mongos> sh.enableSharding("test")
{ "ok" : 0, "errmsg" : "already enabled" }
mongos> sh.status()
--- Sharding Status ---
  sharding version: {
	"_id" : 1,
	"minCompatibleVersion" : 5,
	"currentVersion" : 6,
	"clusterId" : ObjectId("55f5be9a47f10f5ff0d0681e")
}
  shards:
	{  "_id" : "a",  "host" : "a/Anyis-MacBook-Pro.local:27000,Anyis-MacBook-Pro.local:27001,Anyis-MacBook-Pro.local:27002" }
	{  "_id" : "b",  "host" : "b/Anyis-MacBook-Pro.local:27100,Anyis-MacBook-Pro.local:27101,Anyis-MacBook-Pro.local:27102" }
	{  "_id" : "c",  "host" : "c/Anyis-MacBook-Pro.local:27200,Anyis-MacBook-Pro.local:27201,Anyis-MacBook-Pro.local:27202" }
	{  "_id" : "d",  "host" : "d/Anyis-MacBook-Pro.local:27300,Anyis-MacBook-Pro.local:27301,Anyis-MacBook-Pro.local:27302" }
  balancer:
	Currently enabled:  yes
	Currently running:  no
	Failed balancer rounds in last 5 attempts:  0
	Migration Results for the last 24 hours:
		No recent migrations
  databases:
	{  "_id" : "admin",  "partitioned" : false,  "primary" : "config" }
	{  "_id" : "test",  "partitioned" : true,  "primary" : "a" }
```
---

## Week 5: Replication Part 2

Optimizing and monitoring your Replica Sets

#### Replica set config options

+ arbiterOnly: true
	The arbiter server doesn't have any data and is used for election. No data is written or read on the arbiter. Protect network splits.

+ priority: 0(never priority)
	The higher the number, the higher the priority.(e.g. 1.05 has higher priority than 1). The value must be >= 0.

+ hidden: true
	The clients cannot see the server if `hidden: true`.

+ slaveDelay: 8 * 3600 (seconds, that is 8 hours)
	A member must wait x seconds. It's good for preventing against a new client application release bug and getting a view of the DB between backups


### Set up the same replica set as in Week 4, and change the third server (27003) to slaveDelay:true

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5$ mkdir 1 2 3
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5$ ls
1/ 2/ 3/
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5$ mongod --port 27001 --replSet M102P --dbpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5/1 --logpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5/log.1 --logappend --oplogSize 50 --smallfiles --fork
about to fork child process, waiting until server is ready for connections.
forked process: 22503
child process started successfully, parent exiting
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5$ mongod --port 27002 --replSet M102P --dbpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5/2 --logpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5/log.2 --logappend --oplogSize 50 --smallfiles --fork
about to fork child process, waiting until server is ready for connections.
forked process: 22510
child process started successfully, parent exiting
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5$ mongod --port 27003 --replSet M102P --dbpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5/3 --logpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5/log.3 --logappend --oplogSize 50 --smallfiles --fork
about to fork child process, waiting until server is ready for connections.
forked process: 22517
child process started successfully, parent exiting
```

Check replica set status

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5$ ps ax | grep mongo | grep M102P
22503   ??  S      0:00.77 mongod --port 27001 --replSet M102P --dbpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5/1 --logpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5/log.1 --logappend --oplogSize 50 --smallfiles --fork
22510   ??  S      0:00.63 mongod --port 27002 --replSet M102P --dbpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5/2 --logpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5/log.2 --logappend --oplogSize 50 --smallfiles --fork
22517   ??  S      0:00.54 mongod --port 27003 --replSet M102P --dbpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5/3 --logpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5/log.3 --logappend --oplogSize 50 --smallfiles --fork
```

Change 27003 to `slaveDelay: true`:

```
M102P:PRIMARY> var cfg = rs.conf()
M102P:PRIMARY> cfg.members[2].priority = 0;
M102P:PRIMARY> cfg.members[2].slaveDelay = 120;
true
M102P:PRIMARY> cfg
{
	"_id" : "M102P",
	"version" : 1,
	"members" : [
		{
			"_id" : 0,
			"host" : "Anyis-MacBook-Pro.local:27001",
			"arbiterOnly" : false,
			"buildIndexes" : true,
			"hidden" : false,
			"priority" : 1,
			"tags" : {

			},
			"slaveDelay" : 0,
			"votes" : 1
		},
		{
			"_id" : 1,
			"host" : "Anyis-MacBook-Pro.local:27002",
			"arbiterOnly" : false,
			"buildIndexes" : true,
			"hidden" : false,
			"priority" : 1,
			"tags" : {

			},
			"slaveDelay" : 0,
			"votes" : 1
		},
		{
			"_id" : 2,
			"host" : "Anyis-MacBook-Pro.local:27003",
			"arbiterOnly" : false,
			"buildIndexes" : true,
			"hidden" : false,
			"priority" : 0,
			"tags" : {

			},
			"slaveDelay" : 120,
			"votes" : 1
		}
	],
	"settings" : {
		"chainingAllowed" : true,
		"heartbeatTimeoutSecs" : 10,
		"getLastErrorModes" : {

		},
		"getLastErrorDefaults" : {
			"w" : 1,
			"wtimeout" : 0
		}
	}
}

```

Commands to check the status of replic set:

```
rs.status()
rs.isMaster()
rs.config()
```

Get connected to server 2 in the terminal:

```
var server2 = new Mongo('localhost:27002')
var server2_test = server2.getDB('test')
server2_test
server2_test.getMongo().setSlaveOk()
server2_test.foo.findOne()
```

To check which port I am on: `db.isMaster().me`

```
M102P:PRIMARY> db.isMaster().me
Anyis-MacBook-Pro.local:27001
```

### Write concern

+ no call to GLE(getLastError) (no user impact)
+ w : 1 (if any of the server has the data, then an acknowledgement is sent)
+ w : 'majority' (most things, important)
+ w : 3 (all servers,flow control)

### getLastError()

```
getLastError({w:'majority',wtimeout:8000(8 seconds)})
```

### Recommended configs for replica set in a single & multiple datacenters

replica set limits:

+ <= 12 members
+ <= 7 voters

replica sets can mix different storage engines, and the results will be the same

---

## Week 4: Replication

Statement-based vs. Binary replication

Replica Set:

+ Automatic failover
+ Automatic recovery after failover
+ Rollback: A secondary (that was previously a primary) contains write operations that are ahead of the current primary
+ Each server in the replica set needs a unique `dbpath`(to store the data) and `port`


### Starting a Replica Set

The first replica set:

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4$ mkdir 1 2 3

Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4$ mongod --port 27001 --replSet M102P --dbpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/1 --logpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/log.1 --logappend --oplogSize 50 --smallfiles --fork
```

Then do the same for the other 2 slave servers, changing 1 to 2 and 3:

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4$ mongod --port 27002 --replSet M102P --dbpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/2 --logpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/log.2 --logappend --oplogSize 50 --smallfiles --fork
about to fork child process, waiting until server is ready for connections.
forked process: 30005
child process started successfully, parent exiting
```

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4$ mongod --port 27003 --replSet M102P --dbpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/3 --logpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/log.3 --logappend --oplogSize 50 --smallfiles --fork
about to fork child process, waiting until server is ready for connections.
forked process: 30054
child process started successfully, parent exiting
```

Check the replica set is working:

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4$ ps ax | grep mongo | grep M102P

29919   ??  S      0:02.42 mongod --port 27001 --replSet M102P --dbpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/1 --logpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/log.1 --logappend --oplogSize 50 --smallfiles --fork
30005   ??  S      0:00.94 mongod --port 27002 --replSet M102P --dbpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/2 --logpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/log.2 --logappend --oplogSize 50 --smallfiles --fork
30054   ??  S      0:00.40 mongod --port 27003 --replSet M102P --dbpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/3 --logpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/log.3 --logappend --oplogSize 50 --smallfiles --fork
```

Check the first log

```
less log.1
```

### Initiate the replica set

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4$ mongo --port 27001
MongoDB shell version: 3.0.3
connecting to: 127.0.0.1:27001/test
> cfg = {
... _id : "M102P",
... members: [
... {_id:0, host:"Anyis-MacBook-Pro.local:27001"},
... {_id:1, host:"Anyis-MacBook-Pro.local:27002"},
... {_id:2, host:"Anyis-MacBook-Pro.local:27003"}
... ]
... }


{
	"_id" : "M102P",
	"members" : [
		{
			"_id" : 0,
			"host" : "Anyis-MacBook-Pro.local:27001"
		},
		{
			"_id" : 1,
			"host" : "Anyis-MacBook-Pro.local:27002"
		},
		{
			"_id" : 2,
			"host" : "Anyis-MacBook-Pro.local:27003"
		}
	]
}
> rs.initiate(cfg)
{ "ok" : 1 }

```


### Replica Set Status


```
> rs.status()

M102P:OTHER> rs.status()
{
	"set" : "M102P",
	"date" : ISODate("2015-08-30T03:38:27.039Z"),
	"myState" : 1,
	"members" : [
		{
			"_id" : 0,
			"name" : "Anyis-MacBook-Pro.local:27001",
			"health" : 1,
			"state" : 1,
			"stateStr" : "PRIMARY",
			"uptime" : 30456,
			"optime" : Timestamp(1440905869, 1),
			"optimeDate" : ISODate("2015-08-30T03:37:49Z"),
			"electionTime" : Timestamp(1440905873, 1),
			"electionDate" : ISODate("2015-08-30T03:37:53Z"),
			"configVersion" : 1,
			"self" : true
		},
		{
			"_id" : 1,
			"name" : "Anyis-MacBook-Pro.local:27002",
			"health" : 1,
			"state" : 2,
			"stateStr" : "SECONDARY",
			"uptime" : 37,
			"optime" : Timestamp(1440905869, 1),
			"optimeDate" : ISODate("2015-08-30T03:37:49Z"),
			"lastHeartbeat" : ISODate("2015-08-30T03:38:25.183Z"),
			"lastHeartbeatRecv" : ISODate("2015-08-30T03:38:25.183Z"),
			"pingMs" : 0,
			"configVersion" : 1
		},
		{
			"_id" : 2,
			"name" : "Anyis-MacBook-Pro.local:27003",
			"health" : 1,
			"state" : 2,
			"stateStr" : "SECONDARY",
			"uptime" : 37,
			"optime" : Timestamp(1440905869, 1),
			"optimeDate" : ISODate("2015-08-30T03:37:49Z"),
			"lastHeartbeat" : ISODate("2015-08-30T03:38:25.183Z"),
			"lastHeartbeatRecv" : ISODate("2015-08-30T03:38:25.183Z"),
			"pingMs" : 0,
			"configVersion" : 1
		}
	],
	"ok" : 1
}
M102P:PRIMARY>

```

### Replica Set Commands


+ rs.conf()

```
M102P:PRIMARY> rs.conf()
{
	"_id" : "M102P",
	"version" : 1,
	"members" : [
		{
			"_id" : 0,
			"host" : "Anyis-MacBook-Pro.local:27001",
			"arbiterOnly" : false,
			"buildIndexes" : true,
			"hidden" : false,
			"priority" : 1,
			"tags" : {

			},
			"slaveDelay" : 0,
			"votes" : 1
		},
		{
			"_id" : 1,
			"host" : "Anyis-MacBook-Pro.local:27002",
			"arbiterOnly" : false,
			"buildIndexes" : true,
			"hidden" : false,
			"priority" : 1,
			"tags" : {

			},
			"slaveDelay" : 0,
			"votes" : 1
		},
		{
			"_id" : 2,
			"host" : "Anyis-MacBook-Pro.local:27003",
			"arbiterOnly" : false,
			"buildIndexes" : true,
			"hidden" : false,
			"priority" : 1,
			"tags" : {

			},
			"slaveDelay" : 0,
			"votes" : 1
		}
	],
	"settings" : {
		"chainingAllowed" : true,
		"heartbeatTimeoutSecs" : 10,
		"getLastErrorModes" : {

		},
		"getLastErrorDefaults" : {
			"w" : 1,
			"wtimeout" : 0
		}
	}
}
```

+ db.isMaster()

```
M102P:PRIMARY> db.isMaster()
{
	"setName" : "M102P",
	"setVersion" : 1,
	"ismaster" : true,
	"secondary" : false,
	"hosts" : [
		"Anyis-MacBook-Pro.local:27001",
		"Anyis-MacBook-Pro.local:27002",
		"Anyis-MacBook-Pro.local:27003"
	],
	"primary" : "Anyis-MacBook-Pro.local:27001",
	"me" : "Anyis-MacBook-Pro.local:27001",
	"electionId" : ObjectId("55e27a91e44b7dcc992dda69"),
	"maxBsonObjectSize" : 16777216,
	"maxMessageSizeBytes" : 48000000,
	"maxWriteBatchSize" : 1000,
	"localTime" : ISODate("2015-08-30T04:14:44.038Z"),
	"maxWireVersion" : 3,
	"minWireVersion" : 0,
	"ok" : 1
}
```

Which command prevents a node from becoming primary for 5 minutes?

+ rs.stepDown(300)
+ rs.freeze(300)


### Reading & Writing

Error message: 

```
M102P:SECONDARY> db.foo.find()
Error: error: { "$err" : "not master and slaveOk=false", "code" : 13435 }
```

MongoDB doesn't by default allow reading directly from secondary DBs. To enable that, use `rs.slaveOk()`

```
M102P:SECONDARY> rs.slaveOk()
M102P:SECONDARY> db.foo.find()
{ "_id" : ObjectId("55e28423974a3b262098f11e"), "name" : "first" }
```

### Failover

---

## Week 3: Performance

Storage Engines:

+ MMAPv1
+ WiredTiger

Check which storage engine I'm using:

```
db.serverStatus()
```

Explicitly use MMAPv1 or Wired Tiger for storage engine:

```
mongod --storageEngine mmapv1
mongod --storageEngine wiredTiger
```

### MMAPv1:

Power of Two Sized Allocations(with paddings)

### WiredTiger:

Compression + Document-level concurrency control

### createIndex(),getIndexes(),dropIndex()

use $elemMatch for compound indexes

```
db.foo.createIndex({ a: 1, b: 1});

compound index:
db.foo.createIndex({a.b:1});

db.foo.find().sort({ a: 1, b: 1},{unique:true});

db.foo.getIndexes();

db.foo.dropIndex({ a : 1});
```

## Indexes

### Unique index

```
db.foo.find().sort({ a: 1, b: 1},{unique:true});
```
### Sparse index (when majority of the collection don't have that field) 

```
db.foo.find().sort({ a: 1, b: 1},{sparse:true});
```

### TTL index: time to live

```
expireAfterSeconds: 3600
```

### Geospatial indexes

```
{"_id":ObjectId("xxxxxxx"),"loc":[-20,23]}
db.places.createIndex( { loc : "2dsphere" } )
```

### Sort points by nearest distance

```
db.places.find(
	{ loc: 
		{ $near : {
					{ $geometry: 
						{type: "Point", coordinates: [2,2.01]},
						spherical : true
					}
				}
		}
	}
)
```

Find everything within the polygon:

### Text Search Index

db.sentences.createIndex( { words: "text" } )

db.sentences.find( {$text: { $search: "cat"} }, {score: {$meta: "textScore"}}).sort( {score: {$meta: "textScore"}})

### Explain() with executionStats, allPlansExecution

```
db.foo.explain().find()
db.foo.explain("executionStats")
db.foo.explain("allPlansExecution").find( {a:14,b:12} )
```

### Covered Queries

```
db.exp.find({ a:7,b:7,c:7]}, {_id:0,a:2,b:1,c:1})
```

### Read & Write Recap

More indexes = Faster Read = Slower Write

### currentOp() & killOp()

```
db.currentOp()
db.killOp(<opid>)
```

### Profiler

```
show profile

db.showProfilingStatus()
db.setProfilingLevel(level,<slowms>) 0=off 1=slow 2=all
```

Look at the last item in the profile

```
db.system.profile.find().sort({$natural:-1}).limit(1).pretty()
```

### mongostat() and mongotop()

```
mongostat --port 27003
```


---

## Week 2: CRUD and Administrative Commands

### Insert, update

```
db.sample.insert({a:1})

update([Where],[Do],[upsert],[multi])
db.sample.update({_id:100},{"_id":100,x:"hello"})
```

### Partial Updates

+ $set

+ $push

+ $addToSet

+ $pop

+ $unset


```
t.update({_id:101},{$set:{y:100}})

increment y by 1:
t.update({_id:101},{$inc:{y:1}})

add "hi" to array "arr" with push:
t.update({_id:101},{$push:{arr:"hi"}})
```

### Upsert

```
> db.collection.update( query_document, update_document, options_document )

where options_document contains key:value pairs such as:
multi : true/false, 
upsert : true/false,
writeConcern: document
```

### Remove/Delete

```
db.test.remove({_id:100})
```

### Bulk() write operations: ordered or unordered

```
var bulk = db.items.initializeUnorderedBulkOp();
var b = db.items.initializeOrderedBulkOp();
bulk.insert([some sample inserts here]);
bulk.execute();
```

### Commands

+ getLastError
 
+ isMaster

+ serverStatus
 
+ collection.stats() & collection.drop()
 
+ ensureIndex
 
+ dropIndex
 
+ currentOp
 
+ killOp



```
db.runCommand({getLastError:1,w:2})
```

## Quiz Answers:

```
db.users.remove({"addr.city":"Lyon",registered:false});
db.users.update({"_id":"Jane"},{$push:{"likes":"football"}},true);
```


---


## Week 1: Introduction	

Introduction to MongoDB, key concepts and installing Mongo


```
mkdir data
mongod --dbpath data 
```

And in another terminal window: 

```
mongo
mongoimport --stopOnError --db pcat --collection products < products.json
```

```
mongo localhost/pcat
db.products.find().limit(10).skip(2).toArray()
db.products.find({},{name:1,brand:1})
db.products.count()
```

$Exists:

```
db.products.find({for:{$exists:true}},{name:1,for:1})
```

Find all accessories for a product

```
db.products.find({for:'ac3'})
```

Find all products that are accessory

```
db.products.find({type:'accessory'})
```

Dot notation: find where a=1 inside x:
{x:{a:1,b:2}}

```
db.products.find({"x.a":1})
db.products.find({"y.k.bbb":"hello"})
```


Sort

```
db.products.find({price:{$exists:true}},{name:1, price:1}).sort({price:1})

Sort by last name then first name
db.customers.find().sort({lastName:1, firstName:1})

Sort by author name then date posted
db.blogposts.find().sort({author:1, date_posted:-1})
```

Sample script to populate the database

```
for( var i = 1; i < 20000; i ++){db.test.insert({x:i,y:'hi'});}
```

Sample cursor

```
var cursor = db.test.find();
cursor.hasNext()
cursor.next()
```


Write a query that retrieves documents of type "exam", sorted by score in descending order, skipping the first 50 and showing only the next 20.

```
db.scores.find({"type":"exam"}).sort({"score":-1}).skip(50).limit(20);
```