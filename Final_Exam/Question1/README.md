## Final Exam Question 1

Answer: `6`.


Process:

```
sh a.sh

mongo --shell --port 27003 a.js

> ourinit()
{ "ok" : 1 }
waiting for set to initiate...
{
	"setName" : "z",
	"setVersion" : 1,
	"ismaster" : false,
	"secondary" : true,
	"hosts" : [
		"localhost:27001",
		"localhost:27003"
	],
	"arbiters" : [
		"localhost:27002"
	],
	"me" : "localhost:27003",
	"maxBsonObjectSize" : 16777216,
	"maxMessageSizeBytes" : 48000000,
	"maxWriteBatchSize" : 1000,
	"localTime" : ISODate("2015-09-19T22:06:18.817Z"),
	"maxWireVersion" : 3,
	"minWireVersion" : 0,
	"ok" : 1
}
ok, this member is online now; that doesn't mean all members are
ready yet though.


z:PRIMARY> db.foo.insert({_id:1},{writeConcern:{w:2}})
WriteResult({ "nInserted" : 1 })
z:PRIMARY> db.foo.insert({_id:2},{writeConcern:{w:2}})
WriteResult({ "nInserted" : 1 })
z:PRIMARY> db.foo.insert({_id:3},{writeConcern:{w:2}})
WriteResult({ "nInserted" : 1 })

z:PRIMARY> var a = connect("localhost:27001/admin")
connecting to: localhost:27001/admin

z:PRIMARY> a.shutdownServer()
2015-09-19T23:12:17.980+0100 I NETWORK  DBClientCursor::init call() failed
server should be down...


# on 27003, the new primary 

z:PRIMARY> db.foo.insert({_id:4})
WriteResult({ "nInserted" : 1 })
z:PRIMARY> db.foo.insert({_id:5})
WriteResult({ "nInserted" : 1 })
z:PRIMARY> db.foo.insert({_id:6})
WriteResult({ "nInserted" : 1 })


# Restart the server that was shutdown

Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Final_Exam/Question1$ mongod --fork --logpath a.log --smallfiles --oplogSize 50 --port 27001 --dbpath data/z1 --replSet z
about to fork child process, waiting until server is ready for connections.
forked process: 20264
child process started successfully, parent exiting





Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Final_Exam/Question1$ ps -A | grep mongod
20091 ??         0:07.60 mongod --fork --logpath b.log --smallfiles --oplogSize 50 --port 27002 --dbpath data/z2 --replSet z
20094 ??         0:08.11 mongod --fork --logpath c.log --smallfiles --oplogSize 50 --port 27003 --dbpath data/z3 --replSet z
20264 ??         0:02.33 mongod --fork --logpath a.log --smallfiles --oplogSize 50 --port 27001 --dbpath data/z1 --replSet z
20339 ttys001    0:00.00 grep mongod




# Go into the shell to any member of the set

Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Final_Exam/Question1$ mongo --port 27001
MongoDB shell version: 3.0.3
connecting to: 127.0.0.1:27001/test
z:SECONDARY> rs.status()
{
	"set" : "z",
	"date" : ISODate("2015-09-19T22:26:07.525Z"),
	"myState" : 2,
	"syncingTo" : "localhost:27003",
	"members" : [
		{
			"_id" : 1,
			"name" : "localhost:27001",
			"health" : 1,
			"state" : 2,
			"stateStr" : "SECONDARY",
			"uptime" : 456,
			"optime" : Timestamp(1442700813, 1),
			"optimeDate" : ISODate("2015-09-19T22:13:33Z"),
			"syncingTo" : "localhost:27003",
			"configVersion" : 1,
			"self" : true
		},
		{
			"_id" : 2,
			"name" : "localhost:27002",
			"health" : 1,
			"state" : 7,
			"stateStr" : "ARBITER",
			"uptime" : 455,
			"lastHeartbeat" : ISODate("2015-09-19T22:26:06.968Z"),
			"lastHeartbeatRecv" : ISODate("2015-09-19T22:26:06.149Z"),
			"pingMs" : 0,
			"configVersion" : 1
		},
		{
			"_id" : 3,
			"name" : "localhost:27003",
			"health" : 1,
			"state" : 1,
			"stateStr" : "PRIMARY",
			"uptime" : 455,
			"optime" : Timestamp(1442700813, 1),
			"optimeDate" : ISODate("2015-09-19T22:13:33Z"),
			"lastHeartbeat" : ISODate("2015-09-19T22:26:06.968Z"),
			"lastHeartbeatRecv" : ISODate("2015-09-19T22:26:06.149Z"),
			"pingMs" : 0,
			"electionTime" : Timestamp(1442700380, 1),
			"electionDate" : ISODate("2015-09-19T22:06:20Z"),
			"configVersion" : 1
		}
	],
	"ok" : 1
}
z:SECONDARY> db.foo.find()
Error: error: { "$err" : "not master and slaveOk=false", "code" : 13435 }
z:SECONDARY> rs.slaveOk()
z:SECONDARY> db.foo.find()
{ "_id" : 1 }
{ "_id" : 2 }
{ "_id" : 3 }
{ "_id" : 4 }
{ "_id" : 5 }
{ "_id" : 6 }
```