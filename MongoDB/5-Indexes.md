## Indexes

- **What are indexes?**: Indexes are special data structures that store a small portion of the collection's data set in an easy-to-traverse form. They improve query performance and allow for faster data retrieval.
- **Benefits**:
  - Improve query performance and speed up queries.
  - Allow for efficient sorting and ordering of results.
  - However, too many indexes can decrease write performance due to the overhead of updating the index data structure.

- Without indexes, MongoDB reads all documents (collection scan) and sorts results in memory.
- By default, there is only one index on the `_id` field.
- If we insert or update documents, we need to update the index data structure.
- Write performance can decrease if there are too many indexes.
- Delete unnecessary or redundant indexes.
- **Types of Indexes**:
  - Single Field
  - Compound
  - Multikey (on array fields)

### Single Field Index

- Use `createIndex()` to create a new index in a collection. Include an object that contains the field and sort order.
  ```js
  db.customers.createIndex({ birthdate: 1 })
  ```

- To enforce uniqueness in the index field values, add `{unique:true}` as a second parameter.
  ```js
  db.customers.createIndex({ email: 1 }, { unique: true })
  ```

- MongoDB only creates the unique index if there is no duplication in the field values for the index field(s).
- Use `getIndexes()` to see all the indexes created in a collection.
  ```js
  db.customers.getIndexes()
  ```

- Use `explain()` in a collection when running a query to see the execution plan.
  ```js
  db.customers.explain().find({
    birthdate: { $gt: ISODate("1995-08-01") }
  })
  db.customers.explain().find({
    birthdate: { $gt: ISODate("1995-08-01") }
  }).sort({ email: 1 })
  ```

### Multikey Index

- Use `createIndex()` to create a new index on an array field.
  ```js
  db.customers.createIndex({ accounts: 1 })
  ```

- The maximum number of array fields per multikey index is 1. If an index has multiple fields, only one of them can be an array.

### Compound Indexes

- Index on multiple fields, supports queries that match the prefix of the index fields.
- Use `createIndex()` to create a new index with multiple fields.
  ```js
  db.customers.createIndex({ active: 1, birthdate: -1, name: 1 })
  ```

- The order of the fields matters. Recommended order: Equality, Sort, and Range.
  - **Equality**: Fields that match a single field value.
  - **Sort**: Fields that order the results.
  - **Range**: Fields that filter within a range of values.

- Example:
  ```js
  db.customers.find({
    birthdate: { $gte: ISODate("1977-01-01") },
    active: true
  }).sort({ birthdate: -1, name: 1 })
  ```

  - Efficient index for this query:
  ```js
  db.customers.createIndex({ active: 1, birthdate: -1, name: 1 })
  ```

### Deleting Indexes

- Delete unused or redundant indexes.
- Cannot delete the default index on `_id`.
- Use `hideIndex()` to hide an index temporarily. Unhiding is faster than recreating.
- Delete an index with `dropIndex()`.
  ```js
  db.customers.dropIndex('active_1_birthdate_-1_name_1')
  ```

- In production, it's best to hide an index before deleting it.
- `dropIndexes()` deletes all indexes except the default `_id` index.
  ```js
  db.customers.dropIndexes()
  ```

- `dropIndexes(indexName)` can delete multiple indexes.
  ```js
  db.collection.dropIndexes(['index1name', 'index2name', 'index3name'])
  ```

- Use `getIndexes()` to see all the indexes created in a collection.
  ```js
  db.customers.getIndexes()
  ```

- Delete index by key:
  ```js
  db.customers.dropIndex({ active: 1, birthdate: -1, name: 1 })
  ```

## MongoDB Docs
- [Indexes Docs](https://www.mongodb.com/docs/manual/indexes/)