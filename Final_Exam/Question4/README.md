## Final Exam Question 4

Answer: `233`


Process:

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Final_Exam/Question2$ mongo --port 27001
MongoDB shell version: 3.0.3
connecting to: 127.0.0.1:27001/test
z:PRIMARY> cfg = rs.conf()
{
	"_id" : "z",
	"version" : 1,
	"members" : [
		{
			"_id" : 1,
			"host" : "localhost:27001",
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
			"host" : "localhost:27002",
			"arbiterOnly" : true,
			"buildIndexes" : true,
			"hidden" : false,
			"priority" : 1,
			"tags" : {

			},
			"slaveDelay" : 0,
			"votes" : 1
		},
		{
			"_id" : 3,
			"host" : "localhost:27003",
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
z:PRIMARY> cfg.members[2].priority = 0
0



z:PRIMARY> rs.reconfig(cfg)
{ "ok" : 1 }







Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Final_Exam/Question4$ mongo --shell a.js --port 27003
MongoDB shell version: 3.0.3
connecting to: 127.0.0.1:27003/test
type "help" for help
{
	"setName" : "z",
	"setVersion" : 2,
	"ismaster" : false,
	"secondary" : true,
	"hosts" : [
		"localhost:27001"
	],
	"passives" : [
		"localhost:27003"
	],
	"arbiters" : [
		"localhost:27002"
	],
	"primary" : "localhost:27001",
	"passive" : true,
	"me" : "localhost:27003",
	"maxBsonObjectSize" : 16777216,
	"maxMessageSizeBytes" : 48000000,
	"maxWriteBatchSize" : 1000,
	"localTime" : ISODate("2015-09-20T20:34:34.893Z"),
	"maxWireVersion" : 3,
	"minWireVersion" : 0,
	"ok" : 1
}

things to run for the final:
  ourinit()            Initiates the replica set for you
  part4()              Used in problem #4

z:SECONDARY> part4()
233




```
