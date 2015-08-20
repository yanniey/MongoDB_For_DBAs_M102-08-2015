# M102: MongoDB for DBAs

August 2015 with MongoD Version 3.0.3

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