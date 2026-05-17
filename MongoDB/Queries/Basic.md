
- Show databases in the server.
	- Return databases which has at least one collection.
```
show dbs 
```

- Show current selected database.
	- `db` is an object pointing to the current database.
```
db 
```

- Switch or Point to another database.
	- MongoDB uses lazy database creation: If a database name is provided and does not exist, it is created automatically upon the first write operation, and the client context is set to that database.. 
```
use <dbName> 
```

- Drop or delete a database.
	- Deletes the database that the `db` object is pointing to.
```
db.dropDatabase()
```

---
---

- Show collections in the currently pointed database.
```
show collections 
```

- Create a collection.
	- MongoDB follows lazy collection creation: a collection is automatically created when a write operation (e.g., `insertOne`) is performed on a non-existent collection.
	- Check more about [[Write Concern]].
```
db.<collectionName>.insertOne(
	{
		"name": "Sree",
		"age": 30
	}
)

(OR)

db.createCollection("users", {
  capped: <boolean>,
  size: <number>,
  max: <number>,
  validator: <document>,
  validationLevel: <string>,
  validationAction: <string>,
  indexOptionDefaults: <document>,
  storageEngine: <document>,
  collation: <document>,
  writeConcern: <document>,
  timeseries: <document>,
  expireAfterSeconds: <number>,
  clusteredIndex: <document>,
  changeStreamPreAndPostImages: <document>
})

1. Capped Collection Options: Used for fixed-size collections (like logs).
	- FIFO (Old docs overwritten)
	- High performance for append-heavy workloads
	{
		capped: true,
		size: 1048576, // required (bytes)
		max: 10000     // optional (max documents)
	}

2. Schema Validation: Enforce structure at DB level.
	- Prevents bad data
	- Works well with APIs
	{
		validator: {
			$jsonSchema: {
				bsonType: "object",
				required: ["name", "age"],
				properties: {
					name: {
						bsonType: "string"
					},
					age: {
						bsonType: "int",
						minimum: 0
					}
				},
				additionalProperties: false
			}
		},
		validationLevel: "strict", // strict | moderate | off
		validationAction: "error"  // error | warn
	} 

3. Index Defaults: Set default options for indexes.
   - Rarely used in practice
	{
		indexOptionDefaults: {
			storageEngine: {}
		}
	}

4. Storage Engine Options: Engine-specific configs. (eg. WiredTiger)
	- Controls compression, performance tuning.
	{
		storageEngine: {
			wiredTiger: {
				configString: "block_compressor=zstd"
			}
		}
	}

5. Collation (String Comparison Rule): For case-insensitive or locale-aware queries.
	- Useful for search features.
	{
		collation: {
			locale: "en",
			strength: 2    // case-insensitive
		}
	}
   
6. Write Concern: Default write behavior.
	- Ensures durability
	{
		writeConcern: {
			w: "majority",
			j: true,
			wtimeout: 5000
		}
	}

7. Time Series Collections: For metrics, IoT, logs.
	- Optimized storage
	- Built in TTL
	{
		timeSeries: {
			timeField: "timestamp",
			metaField: "deviceId",
			granularity: "seconds"    // seconds | minutes | hours
		},
		expireAfterSeconds: 3600
	}

8. Clustered Collections: Stores documents ordered by a key (like primary key index).
	- Better range queries
	- Reduces index overhead
	{
		clusteredIndex: {
			key: { _id: 1 },
			unique: true
		}
	}

9. Change Stream Pre/Post Images: For tracking document changes.
	- Useful for audit logs/CDC
	{
		changeStreamPreAndPostImages: {
			enabled: true
		}
	}
```

- Rename a Collection.
```
db.<collectionName>.renameCollection(<newCollectionName>);
```

- Modify Collection Settings.
	- In MongoDB, the `collMod` command is used to modify several collection-level options, but not everything.

| Option                                     | Set at Creation | Modifiable (`collMod`) | Immutable  | Notes                                     |
| ------------------------------------------ | --------------- | ---------------------- | ---------- | ----------------------------------------- |
| **validator**                              | ✅               | ✅                      | ❌          | Must redefine full schema when updating   |
| **validationLevel**                        | ✅               | ✅                      | ❌          | `off`, `moderate`, `strict`               |
| **validationAction**                       | ✅               | ✅                      | ❌          | `warn` or `error`                         |
| **capped**                                 | ✅               | ❌                      | ✅          | Cannot convert normal ↔ capped            |
| **size (capped)**                          | ✅               | ⚠️ Limited             | ❌          | Can increase (not always decrease safely) |
| **max (capped)**                           | ✅               | ⚠️ Limited             | ❌          | Same constraint as size                   |
| **writeConcern (default)**                 | ✅               | ❌                      | ✅          | Use per-operation instead                 |
| **collation (default)**                    | ✅               | ❌                      | ✅          | Fixed once created                        |
| **changeStreamPreAndPostImages**           | ❌               | ✅                      | ❌          | Enable/disable via `collMod`              |
| **expireAfterSeconds (TTL index)**         | ❌               | ✅                      | ❌          | Modified via index settings               |
| **index hidden/unhidden**                  | ❌               | ✅                      | ❌          | MongoDB 4.4+                              |
| **timeseries (definition)**                | ✅               | ❌                      | ✅          | Cannot convert normal ↔ timeseries        |
| **timeseries options (granularity, etc.)** | ✅               | ⚠️ Limited             | ❌          | Some fields modifiable in newer versions  |
| **clusteredIndex**                         | ✅               | ❌                      | ✅          | Immutable once set                        |
| **storageEngine options**                  | ✅               | ❌                      | ✅          | Engine-level config                       |
| **sharding (shard key)**                   | ❌               | ❌                      | ⚠️ Complex | Requires resharding, not simple update    |
| **_id index**                              | auto            | ❌                      | ✅          | Always exists, cannot remove              |
| **autoIndexId (deprecated)**               | ✅               | ❌                      | ✅          | Legacy only                               |
| **capped ordering behavior**               | ✅               | ❌                      | ✅          | Fixed FIFO behavior                       |
| **document schema structure**              | ❌               | ✅                      | ❌          | Controlled via validator only             |

```
db.runCommand({
  collMod: "users",
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "age", "email"],  // added new field
      properties: {
        name: {
          bsonType: "string"
        },
        age: {
          bsonType: "int",
          minimum: 1
        },
        email: {                      // new key added
          bsonType: "string",
          pattern: "^.+@.+$"
        }
      }
    }
  },
  validationLevel: "strict",
  validationAction: "error",
  
  index: {
	  name: "name_1",
	  expireAfterSeconds: 3600       // update TTL index
  },
  
  changeStreamPreAndPostImages: {
	  enabled: true
  }
});

- Steps to Copy and recreate collection with new settings.

// 1. Create new collection with desired options
db.createCollection("users_new", { ...newOptions });

// 2. Copy data
db.users.find().forEach(doc => db.users_new.insert(doc));

// 3. Rename collections
db.users.renameCollection("users_backup");
db.users_new.renameCollection("users");
```

- Drop or delete a  collection.
```
db.<collectionName>.drop()
```

---
---

- Insert a document to the collection.
	- The `insertOne()` method is used to insert a single document to a collection in MongoDB.
	- The JavaScript object will be converted to BSON format, and then it will be saved in the MongoDB database.
	- Check more about [[Write Concern]].
```
db.<collectionName>.insertOne(
	{
		"name": "Sree",
		"age": 30
	}
)
```

- Insert multiple documents to the collection.
	- The `insertMany()` method is used to insert multiple documents at once to a collection in MongoDB.
	- They must be passed as an array of JavaScript objects.
	- The JavaScript object will be converted to BSON format, and then it will be saved in the MongoDB database.
	- Check more about [[Write Concern]].
```
// Ordered Insertion
// While inserting the documents, if one document fails then all the following documents insertion will be stopped.
db.<collectionName>.insertMany(
	[
		{
			"name": "Sree",
			"age": 30
		},
		{
			"name": "Dhanush",
			"age": 23
		}
	],
	{
		ordered: true
	}
)

// Unordered Insertion
// While inserting the documents, only those documents with errors will be stopped continuing the insertion of following documents.
db.<collectionName>.insertMany(
	[
		{
			"name": "Sree",
			"age": 30
		},
		{
			"name": "Dhanush",
			"age": 23
		}
	],
	{
		ordered: false
	}
)

```

- Update a document in the collection.
	- Updates only one document (first match).
	- If no match, then it does nothing. Inserts a new document if `upsert` is true.
	- Requires update operators unless doing full replacement.
	- Atomic at document level.
	- Check more about [[Write Concern]].
```
db.<collectionName>.updateOne(
	{ name: "Sree" },
	{ 
		$set: { age: 31 },
		$push: { skills: "MongoDB" }
	},
	{ upsert: true }
)

db.<collectionName>.updateOne(filter, update, options)

1. Query (Filter): Used to specify selection criteria.
	- Same as findOne()

2. Update Document:
	Common Operators:
		$set: Adds new fields or update existing ones.
			db.users.updateOne(
			  { name: "Sree" },
			  { $set: { age: 31, city: "Hyderabad" } }
			);

		$unset: Removes specified fields from the document. It does not set a value to null, rather it completely removes the field from the document.
			db.users.updateOne(
			  { name: "Sree" },
			  { $unset: {              // The value ("", 1, true) is ignored. Only the field name matters.
				  "email": "",         // Direct
				  "address.city": ""   // Object
				  "hobbies.1": ""      // Array (Index is preserved, value becomes null)
				} 
			  }
			);

		$inc: Increments (or dcrements if negative) numeric fields.
			db.users.updateOne(
			  { name: "Sree" },
			  { $inc: { loginCount: 1, age: 1 } }
			);

		$mul: Multiply value
			db.users.updateOne(
			  { name: "Sree" },
			  { $mul: { salary: 1.1 } }
			);

		$rename: Renames a field without changing its value.
			db.users.updateOne(
			  { name: "Sree" },
			  { $rename: { fullname: "name" } }
			);

		$min / $max: Conditional update
		- $min → keeps the smaller value
		- $max → keeps the larger value
			db.users.updateOne(
			  { name: "Sree" },
			  { $min: { age: 25 } }   // updates only if current age > 25
			);
			
			db.users.updateOne(
			  { name: "Sree" },
			  { $max: { age: 35 } }   // updates only if current age < 35
			);

		$currentDate: Set current date
			db.users.updateOne(
			  { name: "Sree" },
			  { $currentDate: { lastModified: true } }
			);
			
			db.users.updateOne(
			  { name: "Sree" },
			  { $currentDate: { lastModified: { $type: "timestamp" } } }
			);

	Array Operators:
		$push: Appends a value to an array. Allows duplicates.
			db.users.updateOne(
			  { "name": "Sree" },
			  { $push: { "skills": "MongoDB" } }
			);

			-- With Modifiers
			db.users.updateOne(
			  { "name": "Sree" },
			  {
			    $push: {
			      "skills": {
			        $each: ["Node.js", "Express"],
			        $sort: 1,        // sort after push
			        $slice: 5,       // limit array size. Positive: Keeps the first 5 elements after pushing. Negative: Keeps the last 5 elements after pushing
			        $position: 0     // insert at index
			      }
			    }
			  }
			);

		$addToSet: Adds only if value does not already exist. Prevents duplicates.
			db.users.updateOne(
			  { "name": "Sree" },
			  { $addToSet: { "skills": "MongoDB" } }
			);

			-- Multiple Values
			db.users.updateOne(
			  { "name": "Sree" },
			  {
			    $addToSet: {
			      "skills": { 
				      $each: ["Node.js", "MongoDB"] 
			      }
			    }
			  }
			);

		$pull: Removes all matching values from array. Does not leave nulls (unlike $unset on arrays)
			db.users.updateOne(
			  { "name": "Sree" },
			  { $pull: { "skills": "MongoDB" } }
			);
			
			-- With Condition
			db.users.updateOne(
			  { "name": "Sree" },
			  {
			    $pull: {
			      "scores": { $lt: 50 }
			    }
			  }
			);

		$pop: Remove First or Last Element.
			db.users.updateOne(
			  { name: "Sree" },
			  { $pop: { skills: 1 } }   // removes last element
			);
			
			db.users.updateOne(
			  { name: "Sree" },
			  { $pop: { skills: -1 } }  // removes first element
			);

	Replacement Document: Replaces entire document (except _id).
		- If the update document does not contain any update operators, MongoDB treats it as a replacement document.
		- This relaces the entire existing ocument, except for the _id field, which remains unchanged.
		db.users.updateOne(
		  { name: "Sree" },
		  {
			name: "Sree",
			age: 31
		  }
		);

	Aggregation Pipeline:
		- Instead of traditional update document, MongDB allows using an aggregation pipeline to compute and update fields dynamically.
		- Each stage processes the document sequentially, similar to the aggregation framework.
		- Not all aggregation stages are allowed (e.g., `$group` is not allowed).
		- Atomic at document level. But not atomic across multiple documents (updateMany).
		[
			{
				$set: {
					age: {
						$add: ["$age", 1]
					}
				}
			}
		]
		-- Both are same
		{ $inc: { age: 1 } }

		-- Advanced: Increments age only if it is ≤ 30
		db.users.updateOne(
		  { name: "Sree" },
		  [
		    {
		      $set: {
		        age: {
		          $cond: {
		            if: { $gt: ["$age", 30] },
		            then: "$age",
		            else: { $add: ["$age", 1] }
		          }
		        }
		      }
		    }
		  ]
		);

3. Options:
	{
		upsert: true,                                // Insert if not found
		writeConcern: { w: "majority" },             // Write acknowledement level
		collation: { locale: "en" },                 // Locale specific string comparison
		arrayFilters: [{ "elem.age": { $gt: 25 } }], // Conditional Array Updates
		hint: { name: 1 }                            // Force index usage
	}
```

- Update multiple documents in the collection.
	- Updates all documents that match the specified filter.
	- It applies the update operation to every matching document in a single command.
	- Check more about [[Write Concern]].
```
db.users.updateMany(
  { role: "admin" },
  { $set: { access: "full" } },
  { upsert: true }
);

db.<collectionName>.updateOne(filter, update, options)

1. Query (Filter): Used to specify selection criteria.
	- Same as findOne()

2. Update Document:
	- Same as updateOne(), Except the Replacement document which is not allowed.

3. Options:
   - Same as updateOne().
```

- Fetch a document from the collection.
	- Returns only one document (first match based on sort or natural order).
	- If no filter is provided, returns the first document in the collection.
	- Unlike `find()`, it does not return a [[Cursor]].
```
db.users.findOne(
  { age: { $gt: 25 } },
  { name: 1, age: 1, _id: 0 },
  { sort: { age: -1 } }
);

---
db.<collectionName>.findOne(query, projection, options)

1. Query (Filter): Used to specify selection criteria.
	Basic: { field: value }
		{ age: 30 }
		{ name: "Sree" }
	Comparison:
		{ age: { $eq: 30 } }
		{ age: { $ne: 30 } }
		{ age: { $gt: 25 } }  
		{ age: { $gte: 25 } }  
		{ age: { $lt: 40 } }  
		{ age: { $lte: 40 } }  
		{ age: { $in: [25, 30] } }  
		{ age: { $nin: [20, 22] } }
	Logical:
		{ $and: [{ age: { $gt: 25 } }, { name: "Sree" }] }
		{ $or: [{ age: 25 }, { age: 30 }] }
		{ $not: { age: { $gt: 30 } } }
		{ $nor: [{ age: 25 }, { name: "Sree" }] }
	Element:
		{ field: { $exists: true } }  
		{ field: { $type: "string" } }
	Evaluation:
		{ field: { $regex: "^S" } }
		{ field: { $mod: [5, 0] } }
		{ $expr: { $gt: ["$age", 25] } }
	Array:
		{ tags: { $all: ["mongodb", "node"] } }
		{ tags: { $size: 2 } }
		{ tags: { $elemMatch: { $eq: "mongodb" } } }
	Nested Field Filtering:
		{ "address.city": "Hyderabad" }

2. Projection: Controls which fields to return.
	{ name: 1, age: 1 }        // include fields
	{ name: 1, _id: 0 }        // exclude _id
	{ password: 0 }            // exclude field

3. Options: Additional controls for query execution. Supports options **inline**
	{
		sort: { age: -1 }, 
		skip: 5,
		hint: { age: 1 },
		maxTimeMS: 1000,
		collation: { locale: "en", strength: 2 },
		readPreference: "secondary",
		readConcern: { level: "majority" },
		comment: "debug query",
		allowPartialResults: true
	}

- findOne() internally behaves like: db.<collectionName>.find().limit(1)
- It returns a document directly, not a cursor.
- You cannot chain cursor methods afterward.
```

- Fetch multiple documents from the collection.
	- Returns a [[Cursor]].
	- `toArray` will iteratively calls next cursors and fetch all the documents.
	- Returns all matching documents.
	- We can use `.pretty()` to format the query result of `.find()`.
```
db.<collectionName>.find(query, projection, options)

1. Query (Filter): Used to specify selection criteria.
	- Same as findOne()

2. Projection: Controls which fields to return.
	- Same as findOne()

3. Options: Additional controls for query execution. Supports options linline or plus cursor chaining.
	- Cursor Chaining
		db.<collectionName>
			.find({ age: { $gt: 25 } })
			.sort({ age: -1 })
			.limit(5)
			.skip(2)
	- Inline
		db.<collectionName>.find(
			{ age: { $gt: 25 } },
			{ name: 1 },
			{
				limit: 5,
				skip: 5,
				sort:  { age: -1 }
			}
		)

db.<collectionName>.find().forEach((doc) => {
	print("UserName: " + doc.name)
})

```

- Delete a document from the collection.
	- `deleteOne` removes a single document that matches the specified filter.
	- If multiple documents match, only the first matching document is deleted.
	- Operation is atomic at document level.
	- No aggregation pipeline support.
	- Always validate filter to avoid unintended deletion.
	- Check more about [[Write Concern]].
```
db.<collectionName>.deleteOne(query, options)

1. Query (Filter): Used to specify selection criteria.
	- Same as findOne()

2. Options: 
	writeConcern: Ensure deletion is acknowledged by majority of nodes.
		{
			writeConcern: { w: "majority" }
		}
	
	collation: Enables case-insensitive matching.
		{
			collation: { locale: "en", strength: 2 }
		}
	
	hint: Forces use of a specific index
		{
			hint: { name: 1 }
		}
```

- Delete multiple documents from the collection.
	- `deleteMany` removes all documents that match the specified filter.
	- If none match -->  no operation.
	- Not atomic across all documents (partial success possible in failures).
	- No aggregation pipeline support. 
	- **NOTE**: If no filter ( { } ) is provided, it deletes all documents in the collection. Full Collection Wipe.
	- Check more about [[Write Concern]].
```
db.<collectionName>.deleteMany(query, options)

1. Query (Filter): Used to specify selection criteria.
	- Same as findOne()

2. Options: 
   - Same as deleteOne()
```
