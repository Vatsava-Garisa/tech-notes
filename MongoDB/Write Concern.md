
**What?** 
----------
- **Write Concern** in MongoDB defines the level of acknowledgement requested from the database for write operations (insert, update, delete). 
- It controls data durability vs performance trade-offs in a distributed system (replica set or sharded cluster).
- Choosing the right level directly impacts data safety, performance, and system reliability.

**How sure do you want to be that your data is safely written?**

**Why?** 
----------
MongoDB is often deployed as a replica set, where data is replicated across multiple nodes:
- Primary → accepts writes
- Secondary → replicate data asynchronously
Without proper write concern:
- Data may be lost during crashes or failovers.
- Applications may assume a write succeeded when it hasn't been safely stored.

**Levels** 
----------
### 1. Unacknowledged (`w: 0`) 
- No confirmation from MongoDB
- Fire and forget writes
- Fastest but risky
```
db.users.insertOne(
	{ name: "Sree" },
	{ writeConcern: { w: 0 } }
)
```
Use case:
- Logging systems where occasional data loss is acceptable
### 2. Acknowledged from Primary only (`w: 1`) 
- This is the default `writeConcern`
- Confirms write reached primary memory
- If primary crashes before replication →  data loss
```
db.users.insertOne(
	{ name: "Sree" },
	{ writeConcern: { w: 1 } }
)
```
### 3. Majority (`w: "majority"`) 
- Acknowledgment from majority of replica set members
- Ensure data survives failover
```
db.users.insertOne(
	{ name: "Sree" },
	{ writeConcern: { w: "majority" } }
)
```
Use case:
- Financial systems
- Orders, payments, critical user data
### 4. Specific Number of Nodes (`w: "n"`) 
- Wait for acknowledgement from specific number of nodes
- In a 3-node replica set → wait for 3 nodes
```
db.users.insertOne(
	{ name: "Sree" },
	{ writeConcern: { w: 3 } }
)
```
Use case:
- Fine-grained durability control

**Options** 
----------
### 1. Journaling (`j: true`) 
- Ensure write is committed to journal (disk)
- Protects against sudden crashes
```
db.users.insertOne(
	{ name: "Sree" },
	{ writeConcern: { w: 1, j: true } }
)
```

### 2. Timeout (`wtimeout: 5000`) 
- Maximum time to wait for acknowledgment
- If timeout occurs → operation may still succeed, but client gets an error
```
db.users.insertOne(
	{ name: "Sree" },
	{ writeConcern: { w: 1, wtimeout: 5000 } }
)
```

**Combined Example** 
- Wait for majority nodes
- Ensure data is written to disk
- Timeout after 3 seconds
```
db.orders.insertOne(
  { item: "Laptop", price: 1000 },
  {
    writeConcern: {
      w: "majority",
      j: true,
      wtimeout: 3000
    }
  }
);
```

**Contexts** 
----------
### 1. Per Operation 
```
db.users.insertOne(
	{ name: "Sree" },
	{ writeConcern: { w: 1 } }
)
```
### 2. Collection Level 
```
db.createCollection("users", {
	writeConcern: { w: 1 }
})
```
### 3. Connection String 
```
mongodb://localhost:27017/?w=majority
```

**Trade-Offs** 
----------

| Setting           | Performance | Durability | Risk                   |
| ----------------- | ----------- | ---------- | ---------------------- |
| w:0               | Highest     | None       | Data loss              |
| w:1               | High        | Low        | Possible loss on crash |
| majority          | Medium      | High       | Safer                  |
| majority + j:true | Lower       | Very High  | Safest                 |
|                   |             |            |                        |

**Examples** 
----------
### Insert 
```
db.orders.insertOne(
  { item: "Laptop", price: 1000 },
  {
    writeConcern: {
      w: "majority",
      j: true,
      wtimeout: 3000
    }
  }
);
```
### Update 
```
db.orders.updateOne(
  { orderId: 101 },
  { $set: { status: "Shipped" } },
  {
    writeConcern: {
      w: "majority",
      j: true,
      wtimeout: 3000
    }
  }
);
```
### Delete 
```
db.users.deleteOne(
  { userId: 101 },
  {
    writeConcern: {
      w: "majority",
      j: true
    }
  }
);
```
