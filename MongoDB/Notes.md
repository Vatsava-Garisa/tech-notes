
MongoDB is a NoSQL database that stores data in a flexible, JSON like format called documents. Documents are fundamental units of data in MongoDB, analogous to rows in relational databases, but far more flexible and expressive.

Collection
----------
A collection is like a table in relational database, which can store related data. But instead of storing data in the form of rows and columns, a collection stores JSON like objects, which is referred to as documents.

Document
----------
- A document is a data structure composed of field-value pairs, similar to a JSON object.
- It is stored in [[BSON]] (Binary JSON) format internally, which allows for faster processing and additional data types not supported by JSON (like Date, Decimal128, etc.).
- All MongoDB documents must have an `_id` field. If it is not explicitly specified, MongoDB auto-generates an ObjectId. The `_id` field is indexed and enforces uniqueness within the collection.
- **Size Limit:** A single document cannot exceed 16 MB in size. If larger, you should use GridFS for storing large files.
- **Schema Less:** MongoDB documents are schema-less. One document can differ with other.
- **Schema Management:** Lack of enforced schema can lead to inconsistent data if not carefully managed.

```
{
	"_id": "12345",
	"name": "Sree",
	"email": "sree@gmail.com",
	"age": 30,
	"address": {
		"street": "123 Street",
		"city": "New York",
		"zip": "10001"
	},
	"skills": ["JS", "TS", "Node.js"]
}
```

- Datatypes
	- Integer
	- String
	- Boolean
	- Array
	- Object
	- Null
	- Date
	- ObjectId
	- Binary Data
	- Regex
	- Double etc.

```
{
	"_id": ObjectId("69983765f2b0ab5669067024"),          - ObjectId
	"name": "Sree",                                       - String
	"age": 30,                                            - Integer
	"isActive": true,                                     - Boolean
	lastLogin: ISODate("2024-12-23T10:30:00Z"),           - Date
	preferences: {                                        - Object
		"theme": "dark",
		"notifications": true
	},
	"skills": ["JS", "TS", "Node.js"],                    - Array
	"profilePicture": BinData(0, "base64encodeddata...")  - Binary Data
}
```

