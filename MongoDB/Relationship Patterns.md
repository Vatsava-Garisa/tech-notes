
**1. One-to-One** 
----------
Usually embed.
```
{
	user: { name: "Sree" },
	profile: { bio: "Developer" }
}
```
**2. One-to-Many** 
----------
- Small (Embed)
```
{
  post: "MongoDB",
  comments: [{ text: "Nice" }, { text: "Good" }]
}
```

- Large (Reference)
```
// posts
{ _id: 1, post: "MongoDB" }

// comments
{ postId: 1, text: "Nice" }
{ postId: 2, text: "Good" }
```
**3. Many-to-Many** 
----------
Often hybrid approach.
```
// students
{ _id: 1, courses: [101, 102] }

// courses
{ _id: 101, students: [1] }

// Hybrid approach
{
  orderId: 1,
  userId: 101,
  items: [
    {
      productId: 1001,     // reference
      name: "Laptop",      // embedded snapshot (Immutable Records)
      price: 1000          // embedded snapshot (Immutable Records)
    }
  ]
}
```
**4. Bucket Pattern** 
----------
The bucket pattern is a data modeling technique where multiple related records are grouped into a single document (bucket) instead of storing each record as a separate document.
- Time-series data (logs, IoT)
- Reduces document count
- Improves write performance
```
{
  sensorId: "s1",
  startTime: "10:00",
  endTime: "10:10",
  readings: [
    { time: "10:00", value: 20 },
    { time: "10:01", value: 22 },
    { time: "10:02", value: 21 }
  ]
}
```
**5. Outlier Pattern** 
----------
The outlier pattern is a data modeling technique where most documents follow a standard structure, but exceptionally large or uncommon data (outliers) are stored separately.
- Optimize for the common case, isolate the rare heavy case.
- Store large data separately.
```
// Main Collection
{
	userId: 2,
	name: "PowerUser",
	hasOutlier: true
}

// Outlier Collection
{  
	userId: 2,  
	posts: [ ... thousands of posts ... ]  
}
```
**6. Extended Reference Pattern** 
----------
Embed only what you need frequently. Reference the rest.
Avoid frequent joins.
- Store the ID for correctness, store key fields for speed.
- If you want sync → need batch updates.
```
{
	orderId: 1,
	user: {
		userId: 1,
		name: "Sree"       // embedded snapshot
	}
}
```
**7. Subset Pattern** 
----------
**Keep frequently used data close, move heavy data away.**
Large documents with frequently accessed subset.
- Faster Reads
- Better Memory Utilization
- Reduced Network Payload
- Scalable Design
Trade-Offs
- Extra query for full data
- Data Fragmentation
- Consistency Management
```
// One Main Collection
{
	product: "Laptop",
	summary: { price: 1000, rating: 4.5 },
	details: { ...large data... }
}
// Fetching this document loads everything
// Wastes memory and banwidth
// Slower queries


// Main Collection (Hot Data)
{
  productId: 1,
  name: "Laptop",
  price: 1000,
  rating: 4.5
}

// Secondary Collection (Cold Data)
{
  productId: 1,
  description: "very long text...",
  reviews: [ ... thousands ... ],
  analytics: { ... }
}
```
**8. Attribute Pattern** 
----------
Dynamic or variable fields are stored as key-value pairs inside an array, instead of fixed fields in the document.
**When** 
- Field names are not known in advance
- Or vary across documents
**Benefits** 
- Flexible schema
- Uniform structure
- Powerful querying
- Index friendly
**Trade-Offs** 
- Query complexity
- Performance overhead
- Less readable
- No type enforcement
**NOTE:** 
- Keep frequently used fields outside.
- Limit attribute size.
- Use consistent naming for keys.
- Use indexes properly.
```
// Why?
{
  name: "Shirt",
  color: "red",
  size: "M",
  material: "cotton"
}

{
  name: "Laptop",
  ram: "16GB",
  cpu: "i7"
}

// Different proucts -> different fields
// Sparse documents
// Difficult indexing
// Schema inconsistency

// Solution
{
  name: "Laptop",
  attributes: [
    { key: "ram", value: "16GB" },
    { key: "cpu", value: "i7" }
  ]
}

{
  name: "T-Shirt",
  attributes: [
    { key: "size", value: "M" },
    { key: "color", value: "red" }
  ]
}

// Querying
db.products.find({
  attributes: {
    $all: [
      { $elemMatch: { key: "color", value: "red" } },
      { $elemMatch: { key: "size", value: "M" } }
    ]
  }
});

// Indexing Strategy (Compound Index)
db.products.createIndex({
  "attributes.key": 1,
  "attributes.value": 1
});

```
**9. Polymorphic Pattern** 
----------
Multiple types of related but structurally different documents are stored in the same collection.
Same collection, different shapes, unified by a **common context**.
- Supports multiple entity types
**Trade-Offs** 
- Schema Complexity
- Sparse Fields
- Index Complexity
- Validation Difficulty
**NOTE** 
- Always use a `type` field. 
- Validate per `type`. 
- Keep shared fields consistent.
```
// Regular user
{
  _id: 1,
  type: "user",
  name: "Sree",
  email: "sree@mail.com"
}

// Admin
{
  _id: 2,
  type: "admin",
  name: "AdminUser",
  permissions: ["ALL"]
}

// Vendor
{
  _id: 3,
  type: "vendor",
  companyName: "ABC Corp",
  products: [ ... ]
}
```

**Real-World Use Cases** 
----------
### A. E-commerce System 
Embed snapshot --> avoids future inconsistency.
- **Products**
```
{
	name: "Laptop",
	price: 1000,
	specs: { ram: "16GB", cpu: "i7" }
}
```
- **Orders (Hybrid Model)**
```
{
	userId: 1,
	items: [
		{
			productId: 101,
			name: "Laptop",
			price: 1000
		}
	]
}
```

### B. Social Media 
Embed comments (small scale).
Reference if comments grow large.
```
{
	userId: 1,
	content: "Hello",
	likes: 100,
	comments: [
		{ userId: 2, text: "Nice" }
	]
}
```

### C. Logging System 
Bucket pattern.
```
{
	service: "auth",
	logs: [
		{ time: "...", msg: "login success" }
	]
}
```

### D. User Profile System 
Embed frequently accessed data.
```
{
	name: 'Sree',
	preferences: { theme: 'dark' },
	activity: [ ... ]
}
```

**Performance Considerations** 
----------
1. Read vs Write Optimization
	- Read-heavy → embed
	- Write-heavy → reference
2. Document Size Limit
	- Max: 16 MB
	- Avoid unbounded arrays
3. Indexing Strategy
	- Index frequently queried fields
	- Compound indexes for multi-field queries
4. Atomicity
	- Atomic at document level
	- Embed related data for atomic updates
5. Avoid Unbounded Arrays
6. Over-normalization
	- Too many reference → slow queries
7. Under-normalization
	- Excess duplication → inconsistency
8. Ignoring Access Patterns
	- Leads to poor performance
