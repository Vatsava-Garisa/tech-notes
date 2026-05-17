
**What?** 
----------
- Journal is MongoDB's durability backbone.
- In MongoDB, the journal is a write-ahead log (WAL) that records changes before they are applied to the main data files on disk.
- It ensures that even if your system crashes unexpectedly, your data can be recovered.

**Journal is a sequential log that ensures durability by persisting write operations safely, even in case of crashes.**

**Why?** 
----------
MongoDB uses RAM for performance. Writes first go to memory and are later flushed to disk.
This introduces a risk:
- If the server crashes before the data is written to disk → data loss
The journal solves this.
- Write intent to a safe place first, then apply changes.

**How?** 
----------
1. Client sends a write operation (insert/update/delete)
2. MongoDB applies it in memory
3. Simultaneously logs the operation in the journal file
4. Journal is flushed to disk periodically (~100ms)
5. Later, data files are updated from memory
```
Client Request
      ↓
Memory (Fast)
      ↓
Journal Log (Disk - Sequential Write)
      ↓
Data Files (Eventually Persisted)
```

**Crash Recovery** 
----------
When MongoDB restarts after a crash:
1. Reads journal files
2. Replays operations
3. Restores consistent state

**Crash Recovery via Journal Replay**

**Journal Commit Interval** 
----------
- Default: ~100 milliseconds
- MongoDB batches writes → improves performance

**Up to 100ms of data could be lost if `j: false`**

**Journaling and Write Concern** 
----------
Journal becomes critical when used with write concern.
```
{ writeConcern: { w: "majority", j: true } }
```
- `w: "majority"` → Majority of the nodes acknowledges
- `j: true` → Data must be written to journal (disk) before acknowledgement

**Journal vs Data Files** 
----------

| Aspect           | Journal           | Data Files      |
| ---------------- | ----------------- | --------------- |
| Purpose          | Durability        | Actual storage  |
| Write Type       | Sequential (fast) | Random (slower) |
| Role             | Safety net        | Final state     |
| Used in recovery | Yes               | No              |


