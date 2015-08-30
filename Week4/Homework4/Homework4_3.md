## Homework 4.3

Answer: `5`

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/Homework4_1$ mongod --dbpath 2 --port 27002 --smallfiles --oplogSize 50 --replSet M102P

Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/Homework4_1$ mongod --dbpath 3 --port 27003 --smallfiles --oplogSize 50 --replSet M102P

```

In another terminal (mongo shell):

```
// Add new members to the replica set

> rs.add("Anyis-MacBook-Pro.local:27002")
{ "ok" : 1 }
> rs.add("Anyis-MacBook-Pro.local:27003")
{ "ok" : 1 }


> rs.slaveOk()
> rs.add("Anyis-MacBook-Pro.local:27003")
{ "ok" : 1 }
> homework.c()
5
```