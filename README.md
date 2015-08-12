# M102: MongoDB for DBAs
August 2015 with MongoD Version 3.0.3

---

## Week 2: CRUD Functions

Insert:

```
db.sample.insert({a:1})
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