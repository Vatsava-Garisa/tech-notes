
**What?** 
----------
- MongoDB stores data in BSON (Binary JSON) format.
- BSON extends JSON by supporting additional data types for efficiency and richer data modeling.
- Each field in a document has a specific type, which affects storage, indexing, and querying.
- It is designed to be more efficient than JSON for storage and transmission, especially in large-scale or high-performance applications.

![[Pasted image 20260415115045.png]]

**String** 
----------
UTF-8 encoded text data.
- User names, emails, addresses, titles, messages etc.
```
{ 
	name: "Sree"
}
```

**Number** 
----------
MongoDB supports multiple numeric types.
**NOTE:** Avoid storing numbers as strings.
### **1. Int32** 
Small integers .
- Age, counts etc.
```
{
	age: NumberInt(30)
}
```
### **2. Int64** 
Large counters.
- Views, transactions etc.
```
{
	views: NumberLong(10000000000)
}
```
### **3. Double** 
- Floating-point values.
```
{
	rating: 4.5
}
```
### **4 Decimal128** 
- Financial data, precision-critical values etc.
```
{
	price: NumberDecimal("199.99")
}
```

**Boolean** 
----------
Represents `true` or `false`.
- Feature flags, status, toggle configurations etc.
```
{
	isActive: true
}
```

**Object (Embedded Document)** 
----------
Nested document inside another document.
Prefer **embedded documents** for related data.
- User profiles, configuration objects, hierarchical data etc.
```
{
	name: "Sree",
	address: {
		city: "Hyderabad",
		pincode: 500001
	}
}
```

**Array** 
----------
Ordered list of values.
- Tags, categories, skills etc.
```
{ 
	skills: [ "JS", "TS", "Node.js" ] 
}
```

**ObjectId** 
----------
12-byte unique identifier automatically generated.
- Primary keys (`_id`). 
- Unique identifiers across distributed systems.
```
{
	_id: ObjectId("507f1f77bcf86cd799439011)
}
```

**Date** 
----------
Stores date and time (milliseconds since epoch).
Use **Date** instead of string timestamps.
- Timestamps (`createdAt`, `updatedAt`), event tracking etc.
```
{
	createdAt: new Date()
}
```

**Timestamp** 
----------
Special internal type (used in replication).
- Oplog (replication), internal operations.
```
{
	ts: Timestamp(1627847283, 1)
}
```

**Null** 
----------
Represents absence of value.
- Optional fields, placeholder for missing data.
```
{
	middleName: null
}
```

**Binary Data** 
----------
Stores raw binary data.
- Images, files, encrypted data, serialized objects etc.
```
{
	file: BinData(0, "...")
}
```

**Regular Expression** 
----------
Pattern matching strings.
- Search functionality, validation, filtering text.
```
{
	name: {
		$regex: "^S"
	}
}
```

**JavaScript Code** 
----------
Stores JS functions/code.
- Stored logic (rarely used in modern apps)
- Legacy MapReduce operations
```
{
	func: function() { return "Hello" }
}
```

**ISODate (Shell Helper)** 
----------
Wrapper for Date in Mongo shell.
- Readable date handling in queries.
- API timestamp storage.
```
{
	createdAt: ISODate("2026-04-15T10:00:00Z)
}
```

**NOTE:** 
----------
### Schema Flexibility 
- MongoDB allows mixing types.
```
{ value: "10" } // string
{ value: 10 }   // number
```

### **Type Impacts Queries** 
```
{ age: "30" } ≠ { age: 30 }
```

### **Indexing Depends on Type** 
- Different types --> different index behavior.
- Important for performance tuning.

### **Embedded Vs Reference** 
- Use Object + Array for:
	- Fast reads
	- Denormalized data

**Example** 
----------
```
{
  _id: ObjectId("..."),
  name: "Sree",
  age: NumberInt(30),
  isActive: true,
  skills: ["Node.js", "MongoDB"],
  salary: NumberDecimal("50000.00"),
  address: {
    city: "Hyderabad",
    pincode: 500001
  },
  createdAt: new Date(),
  lastLogin: null
}
```

