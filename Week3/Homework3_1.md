## Homework 3.1

Answer: `6`

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week3$ mongo --shell localhost/performance performance.js
MongoDB shell version: 3.0.3
connecting to: localhost/performance
type "help" for help
> homework.init()
{
	"connectionId" : 1,
	"n" : 0,
	"syncMillis" : 0,
	"writtenTo" : null,
	"err" : null,
	"ok" : 1
}
still working...
{
	"connectionId" : 1,
	"updatedExisting" : true,
	"n" : 20000,
	"syncMillis" : 0,
	"writtenTo" : null,
	"err" : null,
	"ok" : 1
}
count: 20000



> db.sensor_readings.createIndex({active:1,tstamp:1})
>
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
> db.sensor_readings.getIndexes()
[
	{
		"v" : 1,
		"key" : {
			"_id" : 1
		},
		"name" : "_id_",
		"ns" : "performance.sensor_readings"
	},
	{
		"v" : 1,
		"key" : {
			"active" : 1,
			"tstamp" : 1
		},
		"name" : "active_1_tstamp_1",
		"ns" : "performance.sensor_readings"
	}
]
> homework.a()
6
```