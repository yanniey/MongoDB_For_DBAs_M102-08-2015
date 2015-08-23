## Homework 3.3

Answer:

Q1: `4`
Q2: `4`
Q3: `Yes`

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week3$ mongoimport -d pcat -c products --drop products.json
2015-08-23T00:50:12.598-0500	connected to: localhost
2015-08-23T00:50:12.599-0500	dropping: pcat.products
2015-08-23T00:50:12.600-0500	imported 12 documents

> db.products.createIndex({for:1})
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
> db.products.getIndexes()
[
	{
		"v" : 1,
		"key" : {
			"_id" : 1
		},
		"name" : "_id_",
		"ns" : "pcat.products"
	},
	{
		"v" : 1,
		"key" : {
			"for" : 1
		},
		"name" : "for_1",
		"ns" : "pcat.products"
	}
]

> db.products.find({"for":"ac3"})
{ "_id" : ObjectId("507d95d5719dbef170f15bf9"), "for" : [ "ac3", "ac7", "ac9" ], "name" : "AC3 Series Charger", "price" : 19, "type" : [ "accessory", "charger" ], "warranty_years" : 0.25 }
{ "_id" : ObjectId("507d95d5719dbef170f15bfc"), "available" : false, "color" : "black", "for" : "ac3", "name" : "AC3 Case Black", "price" : 12.5, "type" : [ "accessory", "case" ], "warranty_years" : 0.25 }
{ "_id" : ObjectId("507d95d5719dbef170f15bfb"), "for" : [ "ac3", "ac7", "ac9", "qp7", "qp8", "qp9" ], "name" : "Phone Extended Warranty", "price" : 38, "type" : "warranty", "warranty_years" : 2 }
{ "_id" : ObjectId("507d95d5719dbef170f15bfd"), "available" : true, "color" : "red", "for" : "ac3", "name" : "AC3 Case Red", "price" : 12, "type" : [ "accessory", "case" ], "warranty_years" : 0.25 }
> db.products.find({"for":"ac3"}).count()
4

 db.products.explain("executionStats").find({"for":"ac3"})
{
	"queryPlanner" : {
		"plannerVersion" : 1,
		"namespace" : "pcat.products",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"for" : {
				"$eq" : "ac3"
			}
		},
		"winningPlan" : {
			"stage" : "FETCH",
			"inputStage" : {
				"stage" : "IXSCAN",
				"keyPattern" : {
					"for" : 1
				},
				"indexName" : "for_1",
				"isMultiKey" : true,
				"direction" : "forward",
				"indexBounds" : {
					"for" : [
						"[\"ac3\", \"ac3\"]"
					]
				}
			}
		},
		"rejectedPlans" : [ ]
	},
	"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 4,
		"executionTimeMillis" : 0,
		"totalKeysExamined" : 4,
		"totalDocsExamined" : 4,
		"executionStages" : {
			"stage" : "FETCH",
			"nReturned" : 4,
			"executionTimeMillisEstimate" : 0,
			"works" : 5,
			"advanced" : 4,
			"needTime" : 0,
			"needFetch" : 0,
			"saveState" : 0,
			"restoreState" : 0,
			"isEOF" : 1,
			"invalidates" : 0,
			"docsExamined" : 4,
			"alreadyHasObj" : 0,
			"inputStage" : {
				"stage" : "IXSCAN",
				"nReturned" : 4,
				"executionTimeMillisEstimate" : 0,
				"works" : 5,
				"advanced" : 4,
				"needTime" : 0,
				"needFetch" : 0,
				"saveState" : 0,
				"restoreState" : 0,
				"isEOF" : 1,
				"invalidates" : 0,
				"keyPattern" : {
					"for" : 1
				},
				"indexName" : "for_1",
				"isMultiKey" : true,
				"direction" : "forward",
				"indexBounds" : {
					"for" : [
						"[\"ac3\", \"ac3\"]"
					]
				},
				"keysExamined" : 4,
				"dupsTested" : 4,
				"dupsDropped" : 0,
				"seenInvalidated" : 0,
				"matchTested" : 0
			}
		}
	},
	"serverInfo" : {
		"host" : "Anyis-MacBook-Pro.local",
		"port" : 27017,
		"version" : "3.0.3",
		"gitVersion" : "b40106b36eecd1b4407eb1ad1af6bc60593c6105"
	},
	"ok" : 1
}
```