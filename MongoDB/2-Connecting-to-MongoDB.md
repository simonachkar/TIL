# Connecting to a MongoDB Database
- **Use MongoDB Connection String**: The connection string allows you to connect to MongoDB from Shell, Compass, or any other application
- **Standard Format**: `mongodb://username:password@host:port/database`
- **DNS Seed List Format**: Provides a DNS server list to our connection string, simplifying the configuration for connecting to a MongoDB cluster

**Example Connection String**:
```
mongodb+srv://username:password@cluster0.mongodb.net/mydatabase?retryWrites=true
```
- **Why "srv"**: The `srv` prefix indicates the use of DNS Seed List format
- **Options at the End**: Parameters like `retryWrites=true` control connection behaviors

### Connecting to Cluster via Shell
- **Using mongosh**: A Node.js REPL environment, meaning you can write JavaScript in it

**Example Commands in mongosh**:
```javascript
show dbs; // List all databases
use mydatabase; // Switch to a specific database
db.mycollection.find(); // Find documents in a collection
```

### MongoDB Compass
A GUI for MongoDB that allows you to visualize and interact with your data without writing code

### MongoDB Drivers 

These allow applications to connect to the database using various programming languages. More information is available at mongodb.com/docs/drivers

### Troubleshooting Connection Errors 
- **Network Access Errors**: Ensure your IP is in the network access list
  - Error Example: `MongoServerSelectionError: connection <monitor> to 34.239.188.169:27017 closed`
- **User Authentication Errors**: Ensure credentials are correct
  - Error Example: `MongoServerError: bad auth : Authentication failed`


## MongoDB in Node.js
- An application should use a single `MongoClient` instance for all database requests
- Because creating `MongoClient` instances is resource-intensive and creating a new `MongoClient` for each request will affect performance negatively

<img style="margin-top:20px" src="./assets/2-node-app-using-mongo.png" alt="Example of a Node.js app using MongoDB" />

