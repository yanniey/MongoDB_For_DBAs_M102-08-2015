## Homework 3.2

Answer: `12`

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week3$ mongo --shell localhost/performance performance.js
MongoDB shell version: 3.0.3
connecting to: localhost/performance
type "help" for help
> db.currentOp()
{
	"inprog" : [
		{
			"desc" : "conn3",
			"threadId" : "0x7f844a448df0",
			"connectionId" : 3,
			"opid" : 60054,
			"active" : true,
			"secs_running" : 75,
			"microsecs_running" : NumberLong(75406304),
			"op" : "update",
			"ns" : "performance.sensor_readings",
			"query" : {
				"$where" : "function(){sleep(500);return false;}"
			},
			"client" : "127.0.0.1:63082",
			"numYields" : 149,
			"locks" : {
				"Global" : "w",
				"MMAPV1Journal" : "w",
				"Database" : "w",
				"Collection" : "W"
			},
			"waitingForLock" : false,
			"lockStats" : {
				"Global" : {
					"acquireCount" : {
						"r" : NumberLong(2),
						"w" : NumberLong(150)
					}
				},
				"MMAPV1Journal" : {
					"acquireCount" : {
						"r" : NumberLong(1),
						"w" : NumberLong(164)
					},
					"acquireWaitCount" : {
						"w" : NumberLong(148)
					},
					"timeAcquiringMicros" : {
						"w" : NumberLong(5675)
					}
				},
				"Database" : {
					"acquireCount" : {
						"r" : NumberLong(2),
						"w" : NumberLong(150)
					}
				},
				"Collection" : {
					"acquireCount" : {
						"R" : NumberLong(2),
						"W" : NumberLong(150)
					},
					"acquireWaitCount" : {
						"W" : NumberLong(14)
					},
					"timeAcquiringMicros" : {
						"W" : NumberLong(490)
					}
				}
			}
		}
	]
}
> db.killOp(60054)
{ "info" : "attempting to kill op" }
> homework.c()
12
```