## Homework 4.1

Answer: `5001`

In one terminal:

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4$ mkdir Homework4_1
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4$ cd Homework4_1
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/Homework4_1$ mkdir 1 2 3
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/Homework4_1$ ls
1/ 2/ 3/
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/Homework4_1$ mongod --dbpath 1 --port 27001 --smallfiles --oplogSize 50


2015-08-29T23:49:34.548-0500 E NETWORK  [initandlisten] listen(): bind() failed errno:48 Address already in use for socket: 0.0.0.0:27001
2015-08-29T23:49:34.549-0500 E NETWORK  [initandlisten]   addr already in use
2015-08-29T23:49:34.558-0500 I JOURNAL  [initandlisten] journal dir=1/journal
2015-08-29T23:49:34.558-0500 I JOURNAL  [initandlisten] recover : no journal files present, no recovery needed
2015-08-29T23:49:34.609-0500 I JOURNAL  [durability] Durability thread started
2015-08-29T23:49:34.609-0500 I JOURNAL  [journal writer] Journal writer thread started
2015-08-29T23:49:34.610-0500 I CONTROL  [initandlisten] MongoDB starting : pid=35844 port=27001 dbpath=1 64-bit host=Anyis-MacBook-Pro.local
2015-08-29T23:49:34.610-0500 I CONTROL  [initandlisten] db version v3.0.3
2015-08-29T23:49:34.610-0500 I CONTROL  [initandlisten] git version: b40106b36eecd1b4407eb1ad1af6bc60593c6105
2015-08-29T23:49:34.610-0500 I CONTROL  [initandlisten] build info: Darwin bs-osx108-7 12.5.0 Darwin Kernel Version 12.5.0: Sun Sep 29 13:33:47 PDT 2013; root:xnu-2050.48.12~1/RELEASE_X86_64 x86_64 BOOST_LIB_VERSION=1_49
2015-08-29T23:49:34.610-0500 I CONTROL  [initandlisten] allocator: system
2015-08-29T23:49:34.610-0500 I CONTROL  [initandlisten] options: { net: { port: 27001 }, replication: { oplogSizeMB: 50 }, storage: { dbPath: "1", mmapv1: { smallFiles: true } } }
2015-08-29T23:49:34.611-0500 I INDEX    [initandlisten] allocating new ns file 1/local.ns, filling with zeroes...
2015-08-29T23:49:34.672-0500 I STORAGE  [FileAllocator] allocating new datafile 1/local.0, filling with zeroes...
2015-08-29T23:49:34.672-0500 I STORAGE  [FileAllocator] creating directory 1/_tmp
2015-08-29T23:49:34.719-0500 I STORAGE  [FileAllocator] done allocating datafile 1/local.0, size: 16MB,  took 0.046 secs
2015-08-29T23:49:34.743-0500 I CONTROL  [initandlisten] now exiting
2015-08-29T23:49:34.743-0500 I NETWORK  [initandlisten] shutdown: going to close listening sockets...
2015-08-29T23:49:34.743-0500 I NETWORK  [initandlisten] shutdown: going to flush diaglog...
2015-08-29T23:49:34.743-0500 I NETWORK  [initandlisten] shutdown: going to close sockets...
2015-08-29T23:49:34.743-0500 I STORAGE  [initandlisten] shutdown: waiting for fs preallocator...
2015-08-29T23:49:34.743-0500 I STORAGE  [initandlisten] shutdown: final commit...
2015-08-29T23:49:34.747-0500 I JOURNAL  [initandlisten] journalCleanup...
2015-08-29T23:49:34.748-0500 I JOURNAL  [initandlisten] removeJournalFiles
2015-08-29T23:49:34.748-0500 I JOURNAL  [initandlisten] Terminating durability thread ...
2015-08-29T23:49:34.857-0500 I JOURNAL  [journal writer] Journal writer thread stopped
2015-08-29T23:49:34.857-0500 I JOURNAL  [durability] Durability thread stopped
2015-08-29T23:49:34.858-0500 I STORAGE  [initandlisten] shutdown: closing all files...
2015-08-29T23:49:34.858-0500 I STORAGE  [initandlisten] closeAllFiles() finished
2015-08-29T23:49:34.858-0500 I STORAGE  [initandlisten] shutdown: removing fs lock...
2015-08-29T23:49:34.858-0500 I CONTROL  [initandlisten] dbexit:  rc: 48
```

In another terminal:

```
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4$ cd Homework4_1
Anyi@Anyis-MacBook-Pro:~/Github_Repos/Python/MongoDB_M102_MongoDB_For_DBAs/Week4/Homework4_1$ mongo --port 27001 --shell replication.js
MongoDB shell version: 3.0.3
connecting to: 127.0.0.1:27001/test
type "help" for help
M102P:PRIMARY> homework.init()
ok
M102P:PRIMARY> homework.a()
5001
```