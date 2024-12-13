## Intro to MongoDB

- MongoDB is a general-purpose document database suitable for a wide range of use cases
- Documents offer a flexible and developer-friendly way to work with data
- **Document**: A record in MongoDB, similar to a row in relational (SQL) databases, but more flexible as it can store complex nested data structures
- **Collection**: A grouping of MongoDB documents, similar to a table in relational databases
- **Database**: A container for collections, providing a namespace for data and user access control
- MongoDB database is at the core of **Atlas**, which is a multi-cloud developer platform

## The MongoDB Document Model

- MongoDB documents are displayed in JSON format but stored in BSON (Binary JSON)
- Compared to JSON, BSON supports additional data types like dates, numbers, and ObjectIds
- **ObjectId**: A **data type** used to create unique identifiers for the required `_id` field. If no `_id` is present, MongoDB will create one
- **Flexible schema**: MongoDB allows documents in the same collection to have different structures
- **Polymorphic documents**: Documents in a collection can have different shapes and fields
- **Optional schema validation**: MongoDB allows defining validation rules to ensure data integrity while maintaining flexibility
- **Document values**: Can be any data type, including strings, objects, arrays, booleans, nulls, dates, ObjectIds, etc

**Syntax of a MongoDB Document:**

```
{
  "key": value,
  "key": value,
  "key": value
}
```

**Example:**

```json
{
  "_id": 1,
  "name": "AC3 Phone",
  "colors": ["black", "silver"],
  "price": 200,
  "available": true
}
```

## Data Modeling

### Intro

- **Data Modeling**: The process of creating a data model to define the structure, relationships, and constraints of data
- **Schema**: The relationship of data inside the database
- A good data model can make it easier to manage the data, enable more efficient queries, use less memory and CPU, and reduce costs
- "Data that is accessed together must be stored together"
- *Polymorphism is not schema-less; it's schema-flexible: you define a schema and put validation*
- **Embedded document model**: A way to store related data within a single document

### Data Relationships

> Data that is accessed together should be stored together

#### One-to-One

```bson
    {
      "_id": ObjectId("60c72b2f9af1b2c4d6d5e8e1"),
      "name": "Alice",
      "address": {
        "street": "123 Main St",
        "city": "Anytown"
      }
    }
```

#### One-to-Many

```bson
    {
      "_id": ObjectId("60c72b2f9af1b2c4d6d5e8e2"),
      "name": "Store",
      "products": [
        { "productId": ObjectId("60c72b2f9af1b2c4d6d5e8e3"), "name": "Product 1" },
        { "productId": ObjectId("60c72b2f9af1b2c4d6d5e8e4"), "name": "Product 2" }
      ]
    }
```

#### Many-to-Many

```bson
    {
      "_id": ObjectId("60c72b2f9af1b2c4d6d5e8e5"),
      "studentName": "John",
      "courses": [
        { "courseId": ObjectId("60c72b2f9af1b2c4d6d5e8e6"), "courseName": "Math" },
        { "courseId": ObjectId("60c72b2f9af1b2c4d6d5e8e7"), "courseName": "Science" }
      ]
    }
```

```bson
    {
      "_id": ObjectId("60c72b2f9af1b2c4d6d5e8e6"),
      "courseName": "Math",
      "students": [
        { "studentId": ObjectId("60c72b2f9af1b2c4d6d5e8e5"), "studentName": "John" },
        { "studentId": ObjectId("60c72b2f9af1b2c4d6d5e8e8"), "studentName": "Jane" }
      ]
    }
```

###  Embedding & Referencing
- Two primary ways of modeling data relationships in MongoDB are **embedding** and **referencing**
- **Embedding**: Store related data in a single document (nested document)
  - Suitable for any relationship model (1-1, 1-M, M-M)
  - Simplifies and minimizes queries
  - **Warning**: Can lead to larger, unbounded documents (max 16MB BSON), which is a schema anti-pattern

- **Referencing**: Also known as linking or data normalization
  - Avoids duplication of data
  - Results in smaller documents
  - May require more extensive queries

![image](https://github.com/user-attachments/assets/16a5733a-7344-4705-b4a7-bf2a562fa5a5)

#### Embedding
- Embedding is when related data is stored within a single document
- Example (One-to-Many):
    ```bson
    {
      "_id": ObjectId("60c72b2f9af1b2c4d6d5e8e9"),
      "name": "Blog Post",
      "comments": [
        {
          "commentId": ObjectId("60c72b2f9af1b2c4d6d5e8ea"),
          "text": "Great post!",
          "author": "Alice"
        },
        {
          "commentId": ObjectId("60c72b2f9af1b2c4d6d5e8eb"),
          "text": "Thanks for sharing.",
          "author": "Bob"
        }
      ]
    }
    ```
  
#### Referencing
- Referencing is when related data is stored in separate documents, and references (usually ObjectIds) are used to link them
- Example (One-to-Many):
  ```bson
    // Blog Post document
    {
      "_id": ObjectId("60c72b2f9af1b2c4d6d5e8ec"),
      "name": "Blog Post"
    }

    // Comments documents
    {
      "_id": ObjectId("60c72b2f9af1b2c4d6d5e8ea"),
      "postId": ObjectId("60c72b2f9af1b2c4d6d5e8ec"),
      "text": "Great post!",
      "author": "Alice"
    },
    {
      "_id": ObjectId("60c72b2f9af1b2c4d6d5e8eb"),
      "postId": ObjectId("60c72b2f9af1b2c4d6d5e8ec"),
      "text": "Thanks for sharing.",
      "author": "Bob"
    }
  ```

### Scaling a Data Model

- **Efficiency**: Focus on query result time, memory usage, CPU usage, and storage
- Avoid:
  - Documents larger than 16MB
  - Poor query performance
  - Poor write performance
  - Excessive memory usage
  - Unbounded documents (documents that grow infinitely)

### Schema Anti-Patterns

- **Tools**: Use **Atlas** tools like Data Explorer and Performance Advisor
- **Schema Design Pattern**: Well-defined data structure ensuring optimal performance and scalability
- **Schema Anti-Pattern**: Leads to sub-optimal performance and non-scalable solutions
  - Common issues:
    - Massive arrays
    - Large number of collections
    - Bloated documents
    - Unnecessary indexes
    - Queries without indexes
    - Data accessed together but stored in different collections
- **Atlas Tools**:
  - Data Explorer (available in the free tier)
  - Performance Advisor (available in the M10 tier and up)

## Connecting to a MongoDB Database

- **Use MongoDB Connection String**: The connection string allows you to connect to MongoDB from Shell, Compass, or any other application
- **Standard Format**: `mongodb://username:password@host:port/database`
- **DNS Seed List Format**: Provides a DNS server list to our connection string, simplifying the configuration for connecting to a MongoDB cluster

**Example Connection String**:
```
mongodb+srv://username:password@cluster0.mongodb.net/mydatabase?retryWrites=true
```
- **Why "srv"**: The `srv` prefix indicates the use of DNS Seed List format
- **Options at the End**: Parameters like `retryWrites=true` control connection behaviors

**Connecting to Cluster via Shell**:
- **Using mongosh**: A Node.js REPL environment, meaning you can write JavaScript in it

**Example Commands in mongosh**:
```javascript
show dbs; // List all databases
use mydatabase; // Switch to a specific database
db.mycollection.find(); // Find documents in a collection
```

- **MongoDB Compass**: A GUI for MongoDB that allows you to visualize and interact with your data without writing code

- **MongoDB Drivers**: These allow applications to connect to the database using various programming languages. More information is available at mongodb.com/docs/drivers

### Troubleshooting Connection Errors 
- **Network Access Errors**: Ensure your IP is in the network access list
  - Error Example: `MongoServerSelectionError: connection <monitor> to 34.239.188.169:27017 closed`
- **User Authentication Errors**: Ensure credentials are correct
  - Error Example: `MongoServerError: bad auth : Authentication failed`


## MongoDB in Node.js
- An application should use a single `MongoClient` instance for all database requests.
- Because creating `MongoClient` instances is resource-intensive and creating a new `MongoClient` for each request will affect performance negatively

![image](https://github.com/user-attachments/assets/8db60b74-7535-415f-af96-c11d689ecd67)


## CRUD Operations

### Insert
- **insertOne**: Inserts one doc, automatically creates the collection if it doesn't exist
  - Example:
    ```javascript
    db.collection.insertOne({ name: "John Doe", age: 30 })
    ```
- **insertMany**: Inserts many docs, automatically creates the collection if it doesn't exist
  - Example:
    ```javascript
    db.collection.insertMany([{ name: "Alice" }, { name: "Bob" }])
    ```
  - If you don't provide `_id`, MongoDB will generate one for you

### Find
- **find()**:
  - Example:
    ```javascript
    db.users.find()
    db.users.find({ role: "admin" })
    db.users.find({ age: { $eq: 25 } })
    ```

- **$in Operator**:
  - Matches any of the values specified in an array
  - Syntax:
    ```javascript
    { field: { $in: [value1, value2, ...] } }
    ```
  - Example:
    ```javascript
    db.zips.find({ city: { $in: ["PHOENIX", "CHICAGO"] } })
    ```

- **Comparison Operators**:
  - `$gt` (greater than):
    ```javascript
    db.sales.find({ "items.price": { $gt: 50 } })
    ```
  - `$lt` (less than)
  - `$lte` (less than or equal to)
  - `$gte` (greater than or equal to)

- **Querying on Array Elements**:
  - Normal find:
    ```javascript
    db.accounts.find({ products: "InvestmentFund" })
    ```
  - `$elemMatch`: Querying on array elements with multiple criteria.
    ```javascript
    db.sales.find({
      items: {
        $elemMatch: { name: "laptop", price: { $gt: 800 }, quantity: { $gte: 1 } }
      }
    })
    ```
  - Example:
    This will get all documents where the genre field is equal to either the scalar value of “Historical” or an array that contains “Historical”
    ```javascript
    db.books.find({ genre: "Historical" })
    ```

- **Logical Operators**:
  - **$and**:
    - Example:
      ```javascript
      db.routes.find({
        $and: [{ "airline.name": "Southwest Airlines" }, { stops: { $gte: 1 } }]
      })
      ```
  - **$or**:
    - Example:
      ```javascript
      db.routes.find({
        $or: [{ dst_airport: "SEA" }, { src_airport: "SEA" }]
      })
      ```

- Using `$and` implicitly:
  - Select documents that match multiple expressions
  - Example:
    ```javascript
    db.routes.find({ "airline.name": "Southwest Airlines", stops: { $gte: 1 } })
    ```

### Replace / Update

- **replaceOne()**: Replaces a document with another. 
      - Parameters: filter, replacement, options
  ```javascript
  db.books.replaceOne(
    { _id: ObjectId("6282afeb441a74a98dbbec4e") },
    {
      title: "Data Science Fundamentals for Python and MongoDB",
      // other fields
    }
  )
  ```
- **updateOne()**: Updates a single document. 
   - Update operators: $set, $push
  - Parameters: filter, updates, options
  - **$set**: Adds new fields or replaces existing fields
  - **$push**: Appends a value to an array; if the array is absent, it creates the array field with the value
  - Note: With `updateOne` alone, if the document does not exist, MongoDB won't create it.
  - **upsert**: Option to update or insert the document if it doesn't exist
    ```javascript
    db.podcasts.updateOne(
      { title: "The Developer Hub" },
      { $set: { topics: ["databases", "MongoDB"] } },
      { upsert: true }
    )
    ```
- **findAndModify()**:
  - Returns the document that has just been updated (updateOne + findOne)
  - Guarantees the correct version of the document is returned (in case someone changed it before read).
  - **new** option: If set to true, returns the modified document rather than the original
  - You can use the `upsert` option with it as well

- **updateMany**: Updates multiple documents. Accepts: filter, update, and options
  ```javascript
  db.books.updateMany(
    { publishedDate: { $lt: new Date("2019-01-01") } },
    { $set: { status: "LEGACY" } }
  )
  ```

### Delete

- **deleteOne()**:
  ```javascript
  db.podcasts.deleteOne({ _id: ObjectId("6282c9862acb966e76bbf20a") })
  db.routes.deleteOne({ src_airport: "DEN", dst_airport: "XNA" })
  ```

- **deleteMany()**:
  ```javascript
  db.podcasts.deleteMany({ category: "crime" })
  db.routes.deleteMany({ "airline.name": "Air Berlin" })
  ```

### Modifying Query Results

- **Cursor**: A pointer to the result set of a query. It allows you to iterate over query results one at a time.
- **Cursor Operators**: Methods that modify the behavior of the cursor, such as sorting, limiting, and projecting results

#### Sorting Results

- **`cursor.sort()`**: Sorts the documents in the result set
  - **Syntax**: `db.collection.find(<query>).sort(<sort>)`
  - **Examples**:
    ```javascript
    // Return data on all music companies, sorted alphabetically from A to Z
    db.companies.find({ category_code: "music" }).sort({ name: 1 });

    // Return data on all music companies, sorted alphabetically from Z to A
    db.companies.find({ category_code: "music" }).sort({ name: -1 });
    ```
  - **Explanation**:
    - `1`: Ascending order (smallest to largest, A-Z).
    - `-1`: Descending order (largest to smallest, Z-A).

#### Limiting Results

- **`cursor.limit()`**: Limits the number of documents returned in the result set
  - **Syntax**: `db.collection.find(<query>).limit(<number>)`
  - **Example**:
    ```javascript
    // Return the three music companies with the highest number of employees
    db.companies
      .find({ category_code: "music" })
      .sort({ number_of_employees: -1, _id: 1 })
      .limit(3);
    ```

#### Returning Specific Data from a Query

- **Projection**: Specifies the fields to return in the query results
  - **Syntax**: `db.collection.find(<query>, <projection>)`
  - **Examples**:
    - **Include a Field** (`1` to include):
      ```javascript
      // Return all restaurant inspections - business name, result, and _id fields only.
      db.inspections.find(
        { sector: "Restaurant - 818" },
        { business_name: 1, result: 1 }
      )
      ```
    - **Exclude a Field** (`0` to exclude):
      ```javascript
      // Return all inspections with result of "Pass" or "Warning" - exclude date and zip code.
      db.inspections.find(
        { result: { $in: ["Pass", "Warning"] } },
        { date: 0, "address.zip": 0 }
      )

      // Return all restaurant inspections - business name and result fields only, exclude _id
      db.inspections.find(
        { sector: "Restaurant - 818" },
        { business_name: 1, result: 1, _id: 0 }
      )
      ```
  - **Note**: You can either include or exclude fields in the results, but not both. However, the `_id` field is an exception; it can be suppressed by setting its value to `0` in any projection

#### Counting Documents

- **`countDocuments()`**: Counts the number of documents in the collection that match the query
  - **Syntax**: `db.collection.countDocuments(<query>, <options>)`
  - **Examples**:
    ```javascript
    // Count the number of documents in the trip collection.
    db.trips.countDocuments({})

    // Count the number of trips over 120 minutes by subscribers.
    db.trips.countDocuments({ tripduration: { $gt: 120 }, usertype: "Subscriber" })
    ```

- **General Count**:
  ```javascript
  // Count all documents in the inspections collection
  db.inspections.countDocuments({})
  ```

### Creating Transactions in Node.js Application

- **What are Transactions?**: Transactions are a sequence of operations performed as a single logical unit of work. They ensure that either all operations within the transaction are executed successfully, or none of them are.

- **Multidocument Transactions**: Allow multiple documents across one or more collections to be included in a single transaction, ensuring atomicity.

- **Atomicity**: Ensures that all operations within a transaction are completed successfully; if any operation fails, the transaction is aborted, and all changes are rolled back.

- **Steps to Create a Transaction**:
  1. Start a client session.
  2. Define the transaction options (optional).
  3. Define the sequence of operations.
  4. Release the resources used by the transaction.

- By default, multidocument transactions have a time limit of 60 seconds.
- Pass a session as an option to the operations involved in the transaction.
- Transactions ensure that all operations happen together or not at all.

### Code Example

```js
require('dotenv').config()
const { MongoClient } = require('mongodb')

const uri = process.env.MONGODB_URI
const client = new MongoClient(uri, { useUnifiedTopology: true })

async function runTransaction() {
  try {
    // Collections
    const accounts = client.db("bank").collection("accounts")
    const transfers = client.db("bank").collection("transfers")

    // Account information
    const account_id_sender = "MDB574189300"
    const account_id_receiver = "MDB343652528"
    const transaction_amount = 100

    // Start a session
    const session = client.startSession()

    // Begin a transaction on the session.
    const transactionResults = await session.withTransaction(async () => {
      // Update the balance field of the sender’s account by decrementing the transaction_amount from the balance field.
      const senderUpdate = await accounts.updateOne(
        { account_id: account_id_sender },
        { $inc: { balance: -transaction_amount } },
        { session }
      )

      // Update the balance field of the receiver’s account by incrementing the transaction_amount to the balance field.
      const receiverUpdate = await accounts.updateOne(
        { account_id: account_id_receiver },
        { $inc: { balance: transaction_amount } },
        { session }
      )

      // Create a transfer document and insert it into the transfers collection.
      const transfer = {
        transfer_id: "TR21872187",
        amount: transaction_amount,
        from_account: account_id_sender,
        to_account: account_id_receiver,
      }

      const insertTransferResults = await transfers.insertOne(transfer, { session })

      // Update the transfers_complete array of the sender’s account by adding the transfer_id to the array.
      const updateSenderTransferResults = await accounts.updateOne(
        { account_id: account_id_sender },
        { $push: { transfers_complete: transfer.transfer_id } },
        { session }
      )

      // Update the transfers_complete array of the receiver’s account by adding the transfer_id to the array.
      const updateReceiverTransferResults = await accounts.updateOne(
        { account_id: account_id_receiver },
        { $push: { transfers_complete: transfer.transfer_id } },
        { session }
      )
    })

    // Log a message regarding the success or failure of the transaction.
    if (transactionResults) {
      console.log("Transaction completed successfully.")
    } else {
      console.log("Transaction failed.")
    }
  } catch (err) {
    console.error(`Transaction aborted: ${err}`)
    process.exit(1)
  } finally {
    // End the session and close the client.
    await session.endSession()
    await client.close()
  }
}

// Run the transaction function
client.connect().then(runTransaction).catch(console.error)
```

