# CRUD Operations

- [Insert](#insert)
- [Find](#find)
- [Replace / Update](#replace--update)
- [Delete](#delete)
- [Modifying Query Results](#modifying-query-results)
- [CRUD Operations in Node.js](#crud-operations-in-nodejs)

## Insert
### `insertOne()`
Inserts one doc, automatically creates the collection if it doesn't exist
```js
db.collection.insertOne({ name: "John Doe", age: 30 })
```

### `insertMany()`
Inserts many docs, automatically creates the collection if it doesn't exist
```js
db.collection.insertMany([{ name: "Alice" }, { name: "Bob" }])
```

If you don't provide `_id`, MongoDB will generate one for you (for both `insertOne` and `insertMany`)

## Find

### `find()`
```js
db.users.find()
db.users.find({ role: "admin" })
db.users.find({ age: { $eq: 25 } })
```

### `$in` Operator
Matches any of the values specified in an array
```js
// Syntax
{ field: { $in: [value1, value2, ...] } }

// Example
db.zips.find({ city: { $in: ["PHOENIX", "CHICAGO"] } })
```

### Comparison Operators
- `$gt` (greater than):
  
  ```js
  db.sales.find({ "items.price": { $gt: 50 } })
  ```
- `$lt` (less than)
- `$lte` (less than or equal to)
- `$gte` (greater than or equal to)

### Querying on Array Elements
- Normal find:

  ```js
  db.accounts.find({ products: "InvestmentFund" })
  ```
  
- `$elemMatch`: Querying on array elements with multiple criteria.
  ```js
  db.sales.find({
    items: {
      $elemMatch: {
        name: "laptop",
        price: { $gt: 800 },
        quantity: { $gte: 1 },
      },
    },
  })
  ```
- Example:
  This will get all documents where the genre field is equal to either the value of “Historical” or an array that contains “Historical”
  ```js
  db.books.find({ genre: "Historical" })
  ```

### Logical Operators
- **`$and`**:
    ```js
    db.routes.find({
      $and: [{ "airline.name": "Southwest Airlines" }, { stops: { $gte: 1 } }],
    })
    ```
    
- **Using `$and` implicitly**: Select documents that match multiple expressions
    ```js
    db.routes.find({
        "airline.name": "Southwest Airlines",
        stops: { $gte: 1 },
    })
    ```

- **`$or`**:
    ```js
    db.routes.find({
      $or: [{ dst_airport: "SEA" }, { src_airport: "SEA" }],
    })
    ```

## Replace / Update

### `replaceOne()`
Replaces a document with another - Parameters: filter, replacement, options
```js
db.books.replaceOne(
  { _id: ObjectI("6282afeb441a74a98dbbec4e") },
  {
    title: "Data Science Fundamentalsfor Python and MongoDB",
    // other fields
  }
)
```

### `updateOne()`
Updates a single document
  - Update operators: `$set`, `$push`
  - Parameters: filter, updates, options

#### `$set`
Adds new fields or replaces existing fields
```js
db.customers.updateOne(
  { _id: ObjectId("60c72b2f9af1b2c4d6d5e8e1") },
  { $set: { name: "Alice Smith", age: 30 } }
)
```

#### `$push`
Appends a value to an array; if the array is absent, it creates the array field with the value
```js
db.customers.updateOne(
  { _id: ObjectId("60c72b2f9af1b2c4d6d5e8e1") },
  { $push: { orders: { order_id: 1234, amount: 250 } } }
)
```

#### `upsert`
- Note: With `updateOne` alone, if the document does not exist, MongoDB won't create it.
- **upsert**: Option to update or insert the document if it doesn't exist
```js
db.podcasts.updateOne(
  { title: "The Developer Hub" },
  { $set: { topics: ["databases", "MongoDB"] } },
  { upsert: true }
)
```

### `findAndModify()`
  - Returns the document that has just been updated ( = `updateOne` + `findOne`)
  - Guarantees the correct version of the document is returned (in case someone changed it before read).
  - **`new`** option: If set to true, returns the modified document rather than the original
  - You can use the `upsert` option with it as well
```js
db.tasks.findAndModify({
  query: { _id: ObjectId("60c72b2f9af1b2c4d6d5e8e1") },
  update: { $set: { status: "complete" } },
  new: true // Return the modified document
})
```

### `updateMany()`
Updates multiple documents. Accepts: filter, update, and options
  ```js
  db.books.updateMany(
    { publishedDate: { $lt: new Date("2019-01-01") } },
    { $set: { status: "LEGACY" } }
  )
  ```

## Delete

### `deleteOne()`
  ```js
  db.podcasts.deleteOne({ _id: ObjectId("6282c9862acb966e76bbf20a") })
  db.routes.deleteOne({ src_airport: "DEN", dst_airport: "XNA" })
  ```

### `deleteMany()`
  ```js
  db.podcasts.deleteMany({ category: "crime" })
  db.routes.deleteMany({ "airline.name": "Air Berlin" })
  ```

## Modifying Query Results
- **Cursor**: A pointer to the result set of a query. It allows you to iterate over query results one at a time.
- **Cursor Operators**: Methods that modify the behavior of the cursor, such as sorting, limiting, and projecting results

### Sorting Results `sort()`
**`cursor.sort()`** sorts the documents in the result set
```js
// Syntax:
// db.collection.find(<query>).sort(<sort>)

// Return data on all music companies, sorted alphabetically from A to Z
db.companies.find({ category_code: "music" }).sort({ name: 1 })

// Return data on all music companies, sorted alphabetically from Z to A
db.companies.find({ category_code: "music" }).sort({ name: -1 })
```
`1`: ascending order (smallest to largest, A-Z), `-1`: descending order (largest to smallest, Z-A).

### Limiting Results `limit()`
**`cursor.limit()`**: Limits the number of documents returned in the result set
```js
// Syntax:
// db.collection.find(<query>).limit(<number>)

// Return the three music companies with the highest number of employees
db.companies
  .find({ category_code: "music" })
  .sort({ number_of_employees: -1, _id: 1 })
  .limit(3)
```

### Returning Specific Data from a Query
- **Projection**: Specifies the fields to return in the query results
- **Include a Field** (`1` to include)
- **Exclude a Field** (`0` to exclude)
- **Note**: You can either include or exclude fields in the results, but not both. However, the `_id` field is an exception; it can be suppressed by setting its value to `0` in any projection
```js
// Syntax:
// db.collection.find(<query>, <projection>)

// Return all restaurant inspections - business name, result, and _id fields only.
db.inspections.find(
  { sector: "Restaurant - 818" },
  { business_name: 1, result: 1 }
)

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

### Counting Documents `countDocuments()
Counts the number of documents in the collection that match the query
```js
// Syntax:
// db.collection.countDocuments(<query>, <options>)

// Count the number of documents in the trip collection.
db.trips.countDocuments({})

// Count the number of trips over 120 minutes by subscribers.
db.trips.countDocuments({
  tripduration: { $gt: 120 },
  usertype: "Subscriber",
})
```

## CRUD Operations in Node.js

### Creating Transactions in Node.js Application
- **What are Transactions?**: Transactions are a sequence of operations performed as a single logical unit of work. They ensure that either all operations within the transaction are executed successfully, or none of them are
- **Multidocument Transactions**: Allow multiple documents across one or more collections to be included in a single transaction, ensuring atomicity
- **Atomicity**: Ensures that all operations within a transaction are completed successfully; if any operation fails, the transaction is aborted, and all changes are rolled back
- **Steps to Create a Transaction**:
  1. Start a client session.
  2. Define the transaction options (optional).
  3. Define the sequence of operations.
  4. Release the resources used by the transaction.
- By default, multidocument transactions have a time limit of 60 seconds
- Pass a session as an option to the operations involved in the transaction
- Transactions ensure that all operations happen together or not at all

### Code Example
```js
require("dotenv").config();
const { MongoClient } = require("mongodb");

const uri = process.env.MONGODB_URI;
const client = new MongoClient(uri, { useUnifiedTopology: true });

async function runTransaction() {
  try {
    // Collections
    const accounts = client.db("bank").collection("accounts");
    const transfers = client.db("bank").collection("transfers");

    // Account information
    const account_id_sender = "MDB574189300";
    const account_id_receiver = "MDB343652528";
    const transaction_amount = 100;

    // Start a session
    const session = client.startSession();

    // Begin a transaction on the session.
    const transactionResults = await session.withTransaction(async () => {
      // Update the balance field of the sender’s account by decrementing the transaction_amount from the balance field.
      const senderUpdate = await accounts.updateOne(
        { account_id: account_id_sender },
        { $inc: { balance: -transaction_amount } },
        { session }
      );

      // Update the balance field of the receiver’s account by incrementing the transaction_amount to the balance field.
      const receiverUpdate = await accounts.updateOne(
        { account_id: account_id_receiver },
        { $inc: { balance: transaction_amount } },
        { session }
      );

      // Create a transfer document and insert it into the transfers collection.
      const transfer = {
        transfer_id: "TR21872187",
        amount: transaction_amount,
        from_account: account_id_sender,
        to_account: account_id_receiver,
      };

      const insertTransferResults = await transfers.insertOne(transfer, {
        session,
      });

      // Update the transfers_complete array of the sender’s account by adding the transfer_id to the array.
      const updateSenderTransferResults = await accounts.updateOne(
        { account_id: account_id_sender },
        { $push: { transfers_complete: transfer.transfer_id } },
        { session }
      );

      // Update the transfers_complete array of the receiver’s account by adding the transfer_id to the array.
      const updateReceiverTransferResults = await accounts.updateOne(
        { account_id: account_id_receiver },
        { $push: { transfers_complete: transfer.transfer_id } },
        { session }
      );
    });

    // Log a message regarding the success or failure of the transaction.
    if (transactionResults) {
      console.log("Transaction completed successfully.");
    } else {
      console.log("Transaction failed.");
    }
  } catch (err) {
    console.error(`Transaction aborted: ${err}`);
    process.exit(1);
  } finally {
    // End the session and close the client.
    await session.endSession();
    await client.close();
  }
}

// Run the transaction function
client.connect().then(runTransaction).catch(console.error);
```
