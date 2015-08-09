## Question HW1.2

```
Download the handout. Take a look at its content.

Now, import its contents into MongoDB, into a database called "pcat" and a collection called "products". Use the mongoimport utility to do this.

When done, run this query in the mongo shell:

db.products.find( { type : "case" } ).count()
What's the result?
```

Answer:

In terminal (not in Mongo shell):

```
mongoimport --db pcat --collection products < products.json
```

Then in Mongo shell:

```
use pcat
db.products.find( { type : "case" } ).count()

```

Answer is:

```
3
```
