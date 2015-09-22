## Final Exam Question 8

Answer: `39:15`


Process:

```
mkdir ./data

mkdir ./data/configdb

mongod --configsvr --dbpath ./data/configdb --port 27019

mongorestore ./config_server/ --host localhost:27019

mongo localhost:27019/config

```