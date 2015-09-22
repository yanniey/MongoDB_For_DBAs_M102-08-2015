## Final Exam Question 2

Answer: 

After restarting server on `27003`, the `4`, `5` and `6` writes are not there, but the `last` insert on `27001` is there. 

```

MongoDB preserves the order of writes in a collection in its consistency model. In this problem, 27003's oplog was effectively a "fork" and to preserve write ordering a rollback was necessary during 27003's recovery phase.

```

Process:

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Final_Exam/Question2$ sh a.sh
Already running mongo* processes (this is fyi, should be none probably):
20091 ??         0:12.70 mongod --fork --logpath b.log --smallfiles --oplogSize 50 --port 27002 --dbpath data/z2 --replSet z
20094 ??         0:13.63 mongod --fork --logpath c.log --smallfiles --oplogSize 50 --port 27003 --dbpath data/z3 --replSet z
20264 ??         0:07.61 mongod --fork --logpath a.log --smallfiles --oplogSize 50 --port 27001 --dbpath data/z1 --replSet z
20797 ttys001    0:00.00 grep mongo

make / reset dirs

running mongod processes...
about to fork child process, waiting until server is ready for connections.
forked process: 20804
child process started successfully, parent exiting
about to fork child process, waiting until server is ready for connections.
forked process: 20807
child process started successfully, parent exiting
about to fork child process, waiting until server is ready for connections.
forked process: 20810
child process started successfully, parent exiting

20091 ??         0:12.70 mongod --fork --logpath b.log --smallfiles --oplogSize 50 --port 27002 --dbpath data/z2 --replSet z
20094 ??         0:13.64 mongod --fork --logpath c.log --smallfiles --oplogSize 50 --port 27003 --dbpath data/z3 --replSet z
20264 ??         0:07.61 mongod --fork --logpath a.log --smallfiles --oplogSize 50 --port 27001 --dbpath data/z1 --replSet z
20813 ttys001    0:00.00 grep mongo

Now run:

  mongo --shell --port 27003 a.js






Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Final_Exam/Question2$ mongo --shell --port 27003 a.js
MongoDB shell version: 3.0.3
connecting to: 127.0.0.1:27003/test
type "help" for help
{
	"setName" : "z",
	"setVersion" : 1,
	"ismaster" : true,
	"secondary" : false,
	"hosts" : [
		"localhost:27001",
		"localhost:27003"
	],
	"arbiters" : [
		"localhost:27002"
	],
	"primary" : "localhost:27003",
	"me" : "localhost:27003",
	"electionId" : ObjectId("55fddc5c2ae0f5c359e71604"),
	"maxBsonObjectSize" : 16777216,
	"maxMessageSizeBytes" : 48000000,
	"maxWriteBatchSize" : 1000,
	"localTime" : ISODate("2015-09-19T22:37:33.635Z"),
	"maxWireVersion" : 3,
	"minWireVersion" : 0,
	"ok" : 1
}

things to run for the final:
  ourinit()            Initiates the replica set for you
  part4()              Used in problem #4

z:PRIMARY> ourinit()
{
	"info" : "try querying local.system.replset to see current configuration",
	"ok" : 0,
	"errmsg" : "already initialized",
	"code" : 23
}
waiting for set to initiate...
{
	"setName" : "z",
	"setVersion" : 1,
	"ismaster" : true,
	"secondary" : false,
	"hosts" : [
		"localhost:27001",
		"localhost:27003"
	],
	"arbiters" : [
		"localhost:27002"
	],
	"primary" : "localhost:27003",
	"me" : "localhost:27003",
	"electionId" : ObjectId("55fddc5c2ae0f5c359e71604"),
	"maxBsonObjectSize" : 16777216,
	"maxMessageSizeBytes" : 48000000,
	"maxWriteBatchSize" : 1000,
	"localTime" : ISODate("2015-09-19T22:37:42.699Z"),
	"maxWireVersion" : 3,
	"minWireVersion" : 0,
	"ok" : 1
}
ok, this member is online now; that doesn't mean all members are
ready yet though.
z:PRIMARY> rs.status()
{
	"set" : "z",
	"date" : ISODate("2015-09-19T22:37:50.300Z"),
	"myState" : 1,
	"members" : [
		{
			"_id" : 1,
			"name" : "localhost:27001",
			"health" : 1,
			"state" : 2,
			"stateStr" : "SECONDARY",
			"uptime" : 1157,
			"optime" : Timestamp(1442700813, 1),
			"optimeDate" : ISODate("2015-09-19T22:13:33Z"),
			"lastHeartbeat" : ISODate("2015-09-19T22:37:49.541Z"),
			"lastHeartbeatRecv" : ISODate("2015-09-19T22:37:50.289Z"),
			"pingMs" : 0,
			"syncingTo" : "localhost:27003",
			"configVersion" : 1
		},
		{
			"_id" : 2,
			"name" : "localhost:27002",
			"health" : 1,
			"state" : 7,
			"stateStr" : "ARBITER",
			"uptime" : 1893,
			"lastHeartbeat" : ISODate("2015-09-19T22:37:50.187Z"),
			"lastHeartbeatRecv" : ISODate("2015-09-19T22:37:50.186Z"),
			"pingMs" : 0,
			"configVersion" : 1
		},
		{
			"_id" : 3,
			"name" : "localhost:27003",
			"health" : 1,
			"state" : 1,
			"stateStr" : "PRIMARY",
			"uptime" : 1938,
			"optime" : Timestamp(1442700813, 1),
			"optimeDate" : ISODate("2015-09-19T22:13:33Z"),
			"electionTime" : Timestamp(1442700380, 1),
			"electionDate" : ISODate("2015-09-19T22:06:20Z"),
			"configVersion" : 1,
			"self" : true
		}
	],
	"ok" : 1
}
z:PRIMARY> db.foo.drop()
true
z:PRIMARY> db.foo.insert({_id:1},{writeConcern:{w:2}})
WriteResult({ "nInserted" : 1 })
z:PRIMARY> db.foo.insert({_id:2},{writeConcern:{w:2}})
WriteResult({ "nInserted" : 1 })
z:PRIMARY> db.foo.insert({_id:3},{writeConcern:{w:2}})
WriteResult({ "nInserted" : 1 })
z:PRIMARY> var a = connect("localhost:27001/admin")
connecting to: localhost:27001/admin
z:PRIMARY> a.shutdownServer()
2015-09-19T23:38:41.896+0100 I NETWORK  DBClientCursor::init call() failed
server should be down...
z:PRIMARY> rs.status()
{
	"set" : "z",
	"date" : ISODate("2015-09-19T22:38:44.942Z"),
	"myState" : 1,
	"members" : [
		{
			"_id" : 1,
			"name" : "localhost:27001",
			"health" : 0,
			"state" : 8,
			"stateStr" : "(not reachable/healthy)",
			"uptime" : 0,
			"optime" : Timestamp(0, 0),
			"optimeDate" : ISODate("1970-01-01T00:00:00Z"),
			"lastHeartbeat" : ISODate("2015-09-19T22:38:43.909Z"),
			"lastHeartbeatRecv" : ISODate("2015-09-19T22:38:36.376Z"),
			"pingMs" : 0,
			"lastHeartbeatMessage" : "Failed attempt to connect to localhost:27001; couldn't connect to server localhost:27001 (127.0.0.1), connection attempt failed",
			"configVersion" : -1
		},
		{
			"_id" : 2,
			"name" : "localhost:27002",
			"health" : 1,
			"state" : 7,
			"stateStr" : "ARBITER",
			"uptime" : 1948,
			"lastHeartbeat" : ISODate("2015-09-19T22:38:44.295Z"),
			"lastHeartbeatRecv" : ISODate("2015-09-19T22:38:44.295Z"),
			"pingMs" : 0,
			"configVersion" : 1
		},
		{
			"_id" : 3,
			"name" : "localhost:27003",
			"health" : 1,
			"state" : 1,
			"stateStr" : "PRIMARY",
			"uptime" : 1992,
			"optime" : Timestamp(1442702301, 1),
			"optimeDate" : ISODate("2015-09-19T22:38:21Z"),
			"electionTime" : Timestamp(1442700380, 1),
			"electionDate" : ISODate("2015-09-19T22:06:20Z"),
			"configVersion" : 1,
			"self" : true
		}
	],
	"ok" : 1
}



z:PRIMARY> db.foo.insert({_id:4})
WriteResult({ "nInserted" : 1 })
z:PRIMARY> db.foo.insert({_id:5})
WriteResult({ "nInserted" : 1 })
z:PRIMARY> db.foo.insert({_id:6})
WriteResult({ "nInserted" : 1 })


Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Final_Exam/Question2$ mongod --fork --logpath a.log --smallfiles --oplogSize 50 --port 27001 --dbpath data/z1 --replSet z
about to fork child process, waiting until server is ready for connections.
forked process: 20912
child process started successfully, parent exiting




```