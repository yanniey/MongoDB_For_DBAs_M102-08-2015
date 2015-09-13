about to fork child process, waiting until server is ready for connections.
forked process: 1711
ERROR: child process failed, exited with error number 100
about to fork child process, waiting until server is ready for connections.
forked process: 1714
ERROR: child process failed, exited with error number 100
about to fork child process, waiting until server is ready for connections.
forked process: 1717
ERROR: child process failed, exited with error number 100
about to fork child process, waiting until server is ready for connections.
forked process: 1720
ERROR: child process failed, exited with error number 100
about to fork child process, waiting until server is ready for connections.
forked process: 1723
ERROR: child process failed, exited with error number 100
about to fork child process, waiting until server is ready for connections.
forked process: 1726
ERROR: child process failed, exited with error number 100
about to fork child process, waiting until server is ready for connections.
forked process: 1729
ERROR: child process failed, exited with error number 100
about to fork child process, waiting until server is ready for connections.
forked process: 1732
ERROR: child process failed, exited with error number 100
about to fork child process, waiting until server is ready for connections.
forked process: 1735
ERROR: child process failed, exited with error number 100
about to fork child process, waiting until server is ready for connections.
forked process: 1738
ERROR: child process failed, exited with error number 100
about to fork child process, waiting until server is ready for connections.
forked process: 1741
ERROR: child process failed, exited with error number 100
about to fork child process, waiting until server is ready for connections.
forked process: 1744
ERROR: child process failed, exited with error number 100
about to fork child process, waiting until server is ready for connections.
forked process: 1747
ERROR: child process failed, exited with error number 100
about to fork child process, waiting until server is ready for connections.
forked process: 1750
ERROR: child process failed, exited with error number 100
about to fork child process, waiting until server is ready for connections.
forked process: 1753
ERROR: child process failed, exited with error number 100
about to fork child process, waiting until server is ready for connections.
forked process: 1756
child process started successfully, parent exiting
about to fork child process, waiting until server is ready for connections.
forked process: 1759
child process started successfully, parent exiting
about to fork child process, waiting until server is ready for connections.
forked process: 1762
child process started successfully, parent exiting
about to fork child process, waiting until server is ready for connections.
forked process: 1765
child process started successfully, parent exiting

 1507 ??         0:02.15 mongod --configsvr --dbpath cfg0 --port 26050 --fork --logpath log.cfg0 --logappend
 1510 ??         0:02.04 mongod --configsvr --dbpath cfg1 --port 26051 --fork --logpath log.cfg1 --logappend
 1513 ??         0:02.06 mongod --configsvr --dbpath cfg2 --port 26052 --fork --logpath log.cfg2 --logappend
 1516 ??         0:00.93 mongod --shardsvr --replSet a --dbpath a0 --logpath log.a0 --port 27000 --fork --logappend --smallfiles --oplogSize 50
 1519 ??         0:00.94 mongod --shardsvr --replSet a --dbpath a1 --logpath log.a1 --port 27001 --fork --logappend --smallfiles --oplogSize 50
 1522 ??         0:00.91 mongod --shardsvr --replSet a --dbpath a2 --logpath log.a2 --port 27002 --fork --logappend --smallfiles --oplogSize 50
 1525 ??         0:00.94 mongod --shardsvr --replSet b --dbpath b0 --logpath log.b0 --port 27100 --fork --logappend --smallfiles --oplogSize 50
 1528 ??         0:00.92 mongod --shardsvr --replSet b --dbpath b1 --logpath log.b1 --port 27101 --fork --logappend --smallfiles --oplogSize 50
 1531 ??         0:00.91 mongod --shardsvr --replSet b --dbpath b2 --logpath log.b2 --port 27102 --fork --logappend --smallfiles --oplogSize 50
 1534 ??         0:00.91 mongod --shardsvr --replSet c --dbpath c0 --logpath log.c0 --port 27200 --fork --logappend --smallfiles --oplogSize 50
 1537 ??         0:00.93 mongod --shardsvr --replSet c --dbpath c1 --logpath log.c1 --port 27201 --fork --logappend --smallfiles --oplogSize 50
 1540 ??         0:00.94 mongod --shardsvr --replSet c --dbpath c2 --logpath log.c2 --port 27202 --fork --logappend --smallfiles --oplogSize 50
 1543 ??         0:00.96 mongod --shardsvr --replSet d --dbpath d0 --logpath log.d0 --port 27300 --fork --logappend --smallfiles --oplogSize 50
 1546 ??         0:00.91 mongod --shardsvr --replSet d --dbpath d1 --logpath log.d1 --port 27301 --fork --logappend --smallfiles --oplogSize 50
 1549 ??         0:00.90 mongod --shardsvr --replSet d --dbpath d2 --logpath log.d2 --port 27302 --fork --logappend --smallfiles --oplogSize 50
 1552 ??         0:00.61 mongos --configdb Anyis-MacBook-Pro.local:26050,Anyis-MacBook-Pro.local:26051,Anyis-MacBook-Pro.local:26052 --fork --logappend --logpath log.mongos0
 1555 ??         0:00.57 mongos --configdb Anyis-MacBook-Pro.local:26050,Anyis-MacBook-Pro.local:26051,Anyis-MacBook-Pro.local:26052 --fork --logappend --logpath log.mongos1 --port 26061
 1558 ??         0:00.57 mongos --configdb Anyis-MacBook-Pro.local:26050,Anyis-MacBook-Pro.local:26051,Anyis-MacBook-Pro.local:26052 --fork --logappend --logpath log.mongos2 --port 26062
 1561 ??         0:00.59 mongos --configdb Anyis-MacBook-Pro.local:26050,Anyis-MacBook-Pro.local:26051,Anyis-MacBook-Pro.local:26052 --fork --logappend --logpath log.mongos3 --port 26063
 1767 ttys000    0:00.00 grep mongo

2015-09-13T13:24:19.715-0500 I NETWORK  [conn38] end connection 192.168.1.128:53922 (25 connections now open)
2015-09-13T13:24:19.715-0500 I NETWORK  [conn36] end connection 192.168.1.128:53917 (26 connections now open)
2015-09-13T13:24:19.715-0500 I NETWORK  [conn38] end connection 192.168.1.128:53924 (25 connections now open)

2015-09-13T13:24:18.320-0500 I CONTROL  [initandlisten] dbexit:  rc: 100
2015-09-13T13:24:18.358-0500 I CONTROL  [initandlisten] dbexit:  rc: 100
2015-09-13T13:24:18.400-0500 I CONTROL  [initandlisten] dbexit:  rc: 100
2015-09-13T13:24:18.439-0500 I CONTROL  [initandlisten] dbexit:  rc: 100
2015-09-13T13:24:18.475-0500 I CONTROL  [initandlisten] dbexit:  rc: 100
2015-09-13T13:24:18.515-0500 I CONTROL  [initandlisten] dbexit:  rc: 100
2015-09-13T13:24:18.552-0500 I CONTROL  [initandlisten] dbexit:  rc: 100
2015-09-13T13:24:18.589-0500 I CONTROL  [initandlisten] dbexit:  rc: 100
2015-09-13T13:24:18.629-0500 I CONTROL  [initandlisten] dbexit:  rc: 100
2015-09-13T13:24:18.671-0500 I CONTROL  [initandlisten] dbexit:  rc: 100
2015-09-13T13:24:18.711-0500 I CONTROL  [initandlisten] dbexit:  rc: 100
2015-09-13T13:24:18.753-0500 I CONTROL  [initandlisten] dbexit:  rc: 100

2015-09-13T13:24:19.458-0500 I SHARDING [mongosMain] dbexit:  rc:48
2015-09-13T13:24:19.536-0500 I SHARDING [mongosMain] dbexit:  rc:48
2015-09-13T13:24:19.616-0500 I SHARDING [mongosMain] dbexit:  rc:48
2015-09-13T13:24:19.711-0500 I SHARDING [Balancer] balancer id: Anyis-MacBook-Pro.local:26063 started at Sep 13 13:24:19
