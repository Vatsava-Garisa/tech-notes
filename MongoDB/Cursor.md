
**What?** 
----------
- A cursor in MongoDB is a server-side mechanism for iterating over query results incrementally. 
- Instead of returning the full result set in a single response, MongoDB returns a cursor that allows the client to retrieve documents in controlled batches.
- Aggregation pipelines return aggregation cursors. They behave similarly to query cursors. Process results through pipeline stages before returning.

- **Lazy Loading** → Data fetched only when needed
- **Batch-Oriented** → Data retrieved in chunks
- **Stateful** → Server maintains cursor state

**A cursor is a lazy-evaluated iterator that streams query results from the database to the application.**
**Cursors allow MongoDB to decouple query execution from result consumption, ensuring both performance and scalability.**

**Purpose** 
----------
- **Memory Efficiency**
	- Avoid loading large datasets into memory
- **Network Optimization**
	- Reduce payload size per request
- **Scalability**
	- Enable processing of large collections
- **Responsiveness**
	- Provide initial results quickly

**Internal Working** 
----------
1. Client issues a query.
```
db.users.find({ age: { $gte: 18 } })
```
2. MongoDB
	- Executes the query
	- Returns the first batch of documents (default ~101) with Cursor ID.
3. Client continues iteration.
	- Requests additional batches using the Cursor ID.
4. Cursor lifecycle ends when:
	- All documents are consumed
	- Cursor is explicitly closed
	- Cursor times out

```
Query Execution 
   ↓
Cursor Created (Server)
   ↓
Initial Batch Returned
   ↓
Client Iteration (getMore)
   ↓
Cursor Exhausted / Closed / Timed Out
```

**Types** 
----------
### 1. Non-Tailable Cursor (Default) 
- Standard cursor returned by `find()` and `aggregate()`
- Automatically closes after all results are consumed
### 2. Tailable Cursor 
Applicable only to **capped collections**.
- Remains open after reaching end of data
- Allows reading newly inserted documents
```
db.logs.find().tailable()
```
**Use Cases:** 
- Log streaming
- Event ingestion systems
### 3. Tailable Cursor with `awaitData` 
- Blocks and waits for new data
- Reduces polling overhead
```
db.logs.find().tailable().awaitData()
```

**Methods** 
----------
### 1. Iteration 
```
cursor.hasNext()
cursor.next()
```
### 2. Data Retrieval 
```
db.users.find().toArray()

- Converts entire result set into an array
- Not recommended for large datasets
```
### 3. Result Control 
**Limit** 
```
db.users.find().limit(10)
```
**Skip** 
```
db.users.find().skip(10)
```
**Sort** 
```
db.users.find().sort({ age: -1 })
```
### 4. Batch Control 
```
db.users.find().batchSize(50)

- Controls number of documents fetched per batch
```
### 5. Cursor Behavior 
```
db.users.find().noCursorTimeout()

- Default is 10 minutes of inactivity
- But this method keeps cursor open beyond default timeout
NOTE - Use noCursorTimeout() cautiously to avoid memory leaks
```

**Cursor in Application Code (Node.js)** 
----------
```
const cursor = db.collection('users').find();  
  
for await (const doc of cursor) {  
  console.log(doc);  
}
```

**Advantages:** 
- Async iteration
- Backpressure handling
- Cleaner syntax

