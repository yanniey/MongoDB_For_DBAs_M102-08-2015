# M102: MongoDB for DBAs
August 2015 with MongoD Version 3.0.3

---

Chapter 1: Introduction	Introduction to MongoDB, key concepts and installing Mongo

---

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