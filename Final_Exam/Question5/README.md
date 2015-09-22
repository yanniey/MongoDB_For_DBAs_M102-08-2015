## Final Exam Question 5

Answer: 
```
We can create an index to make the following query fast/faster: db.postings.find( { "comments.flagged" : true } )

One way to assure people vote at most once per posting is to use this form of update: db.postings.update( { _id: . . . }, { $inc : {votes:1}, $push : {voters:'joe'} } ); combined with an index on { voters : 1 } which has a unique key constraint.
```

