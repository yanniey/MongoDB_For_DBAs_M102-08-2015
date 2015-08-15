## Homework 2.2

```
> db.products.insert({"_id" : "ac9", "name" : "AC9 Phone", "brand" : "ACME","type" : "phone", "price" : 333, "warranty_years" : 0.25, "available" : true})
WriteResult({ "nInserted" : 1 })
> myobj = db.products.findOne( { _id : ObjectId("507d95d5719dbef170f15c00") } )
{
	"_id" : ObjectId("507d95d5719dbef170f15c00"),
	"name" : "Phone Service Family Plan",
	"type" : "service",
	"monthly_price" : 90,
	"limits" : {
		"sms" : {
			"n" : "unlimited",
			"over_rate" : 0
		},
		"voice" : {
			"units" : "minutes",
			"n" : 1200,
			"over_rate" : 0.05
		},
		"data" : {
			"n" : "unlimited",
			"over_rate" : 0
		}
	},
	"sales_tax" : true,
	"term_years" : 2
}
> myobj
{
	"_id" : ObjectId("507d95d5719dbef170f15c00"),
	"name" : "Phone Service Family Plan",
	"type" : "service",
	"monthly_price" : 90,
	"limits" : {
		"sms" : {
			"n" : "unlimited",
			"over_rate" : 0
		},
		"voice" : {
			"units" : "minutes",
			"n" : 1200,
			"over_rate" : 0.05
		},
		"data" : {
			"n" : "unlimited",
			"over_rate" : 0
		}
	},
	"sales_tax" : true,
	"term_years" : 2
}
> myobj.term_years = 3
3
> db.products.save(myobj)
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> myobj.limits.sms.over_rate = 0.01
0.01
> db.products.save(myobj)
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> homework.b()
0.050.019031
```