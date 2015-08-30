## Homework 4.2

Answer: `5002`

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/Homework4_1$ ps ax | grep mongod | grep M102P
34769   ??  S      0:54.64 mongod --port 27001 --replSet M102P --dbpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/1 --logpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/log.1 --logappend --oplogSize 50 --smallfiles --fork
34772   ??  S      0:52.07 mongod --port 27002 --replSet M102P --dbpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/2 --logpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/log.2 --logappend --oplogSize 50 --smallfiles --fork
34776   ??  S      0:50.84 mongod --port 27003 --replSet M102P --dbpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/3 --logpath /Users/Anyi/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/log.3 --logappend --oplogSize 50 --smallfiles --fork

// kill replica set that's currently running

Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/Homework4_1$ kill 34769
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/Homework4_1$ kill 34772
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/Homework4_1$ kill 34776

// use --replSet to convert the replica set to a single server 

Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/Homework4_1$ mongod --dbpath 1 --port 27001 --smallfiles --oplogSize 50 --replSet M102P
```

In another terminal: 

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/Homework4_1$ mongo --port 27001 --shell replication.js
MongoDB shell version: 3.0.3
connecting to: 127.0.0.1:27001/test
type "help" for help

M102P:PRIMARY> homework.init()
ok

M102P:PRIMARY> use replication
switched to db replication

M102P:PRIMARY> show dbs
local        0.094GB
replication  0.031GB

M102P:PRIMARY> homework.b()
5002
```