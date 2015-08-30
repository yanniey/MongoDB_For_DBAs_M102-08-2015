## Homework 4.4

Answer: `6`


```

// Step down port 27001 from primary
rs.stepDown()

// Find out which is the new primary and get connrected to it

> rs.status()
2015-08-30T00:32:55.474-0500 I NETWORK  trying reconnect to 127.0.0.1:27001 (127.0.0.1) failed
2015-08-30T00:32:55.474-0500 I NETWORK  reconnect 127.0.0.1:27001 (127.0.0.1) ok
{
	"set" : "M102P",
	"date" : ISODate("2015-08-30T05:32:55.474Z"),
	"myState" : 2,
	"members" : [
		{
			"_id" : 0,
			"name" : "Anyis-MacBook-Pro.local:27001",
			"health" : 1,
			"state" : 2,
			"stateStr" : "SECONDARY",
			"uptime" : 767,
			"optime" : Timestamp(1440912224, 1),
			"optimeDate" : ISODate("2015-08-30T05:23:44Z"),
			"configVersion" : 3,
			"self" : true
		},
		{
			"_id" : 1,
			"name" : "Anyis-MacBook-Pro.local:27002",
			"health" : 1,
			"state" : 1,
			"stateStr" : "PRIMARY",
			"uptime" : 634,
			"optime" : Timestamp(1440912224, 1),
			"optimeDate" : ISODate("2015-08-30T05:23:44Z"),
			"lastHeartbeat" : ISODate("2015-08-30T05:32:53.960Z"),
			"lastHeartbeatRecv" : ISODate("2015-08-30T05:32:54.102Z"),
			"pingMs" : 0,
			"electionTime" : Timestamp(1440912402, 1),
			"electionDate" : ISODate("2015-08-30T05:26:42Z"),
			"configVersion" : 3
		},
		{
			"_id" : 2,
			"name" : "Anyis-MacBook-Pro.local:27003",
			"health" : 1,
			"state" : 2,
			"stateStr" : "SECONDARY",
			"uptime" : 550,
			"optime" : Timestamp(1440912224, 1),
			"optimeDate" : ISODate("2015-08-30T05:23:44Z"),
			"lastHeartbeat" : ISODate("2015-08-30T05:32:53.959Z"),
			"lastHeartbeatRecv" : ISODate("2015-08-30T05:32:53.958Z"),
			"pingMs" : 0,
			"configVersion" : 3
		}
	],
	"ok" : 1
}



Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/Homework4_1$ mongo --port 27002 --shell replication.js
MongoDB shell version: 3.0.3
connecting to: 127.0.0.1:27002/test
type "help" for help




// Remove port 27001 from replica set

M102P:PRIMARY> rs.remove("Anyis-MacBook-Pro.local:27001")
{ "ok" : 1 }
M102P:PRIMARY> homework.d()
6
```