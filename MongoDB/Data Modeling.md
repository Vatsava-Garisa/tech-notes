
**What?** 
----------
Data modeling in MongoDB is the process of structuring documents to optimize for application access patterns, not strict normalization.
Unlike relational databases, MongoDB favors:
- Denormalization
- Flexible schema
- Document-oriented design

**Model your data based on how you query it**
**How will my application read and write this data efficiently at scale?**

**Embedding (Denormalization)** 
----------
Store related data in a single document.
**Advantages**
- Single query --> fast reads
- Atomic updates
- No joins
**Disadvantages**
- Document size growth (16 MB limit)
- Data duplication
- Difficult partial updates at scale

```
{
  userId: "UUID",
  name: "Sree",
  address: {
    city: "Hyderabad",
    pincode: 500001
  }
}
```

**Referencing (Normalization)** 
----------
Store relationships using references (like foreign keys).
**Advantages** 
- Avoid duplication
- Better for large datasets
- Independent scaling
**Disadvantages** 
- Requires multiple queries / `$lookup`
- Slower reads compared to embedding
**Types** 
- Child Referencing - Parent doc stores child doc reference.
- Parent Referencing - Child doc stores parent doc reference.
- Two-way Referencing - Both parent and child doc store references.
```
// users
{ _id: 1, name: "Sree" }

// orders
{ userId: 1, product: "Laptop" }
```

| Use Embedding          | Use Referencing             |
| ---------------------- | --------------------------- |
| One-to-few             | One-to-many (large)         |
| Read-heavy             | Write-heavy                 |
| Data accessed together | Data accessed independently |
| Small bounded data     | Unbounded growth            |

