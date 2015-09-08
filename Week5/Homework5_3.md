## Homework 5.3 Bulk insert query

```
Please insert into the m102 collection, the following 3 documents, using only a one shell query:

{ "a" : 1 }
{ "b" : 2 }
{ "c" : 3 }
```

Answer: 

```
db.m102.insert(
    [
        { "a":1},
        { "b":2},
        { "c":3}
    ]
)
```

