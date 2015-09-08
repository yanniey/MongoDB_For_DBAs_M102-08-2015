## Homework 5.1

```
To demonstrate that you have done this, what is the text in the "state" field for the arbiter when you run rs.status()?
```

Answer: `7`

+ First terminal(run Mongod):

```
mongod --replSet M102P
```

+ Second terminal(add arbiter to replSet):

```
mongod --port 30000 --dbpath  /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week5/arb --replSet M102P
```


+ Third terminal (Mongo shell):

```
mongo

M102P:PRIMARY> rs.addArb("Anyis-MacBook-Pro.local:30000")
{ "ok" : 1 }
```