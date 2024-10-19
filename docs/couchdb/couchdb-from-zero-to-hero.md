# Comprehensive CouchDB Guide

## Table of Contents

1. [Introduction](#introduction)
2. [Installation and Configuration](#installation-and-configuration)
3. [Basic CRUD Operations](#basic-crud-operations)
4. [Mango Queries](#mango-queries)
5. [View Design Documents](#view-design-documents)
6. [Full Text Search](#full-text-search)
7. [Validation Documents](#validation-documents)
8. [Faceted Search](#faceted-search)
9. [Replication and Sync](#replication-and-sync)
10. [Security and Authentication](#security-and-authentication)
11. [Performance Tuning and Best Practices](#performance-tuning-and-best-practices)

## 1. Introduction

CouchDB is a document-oriented NoSQL database that offers a RESTful JSON API, real-time change notifications, and bi-directional replication. This guide will cover advanced topics and provide detailed examples for working with CouchDB.

## 2. Installation and Configuration

### Installation

#### On Ubuntu:

```bash
# Add CouchDB repository
echo "deb https://apache.bintray.com/couchdb-deb focal main" | sudo tee -a /etc/apt/sources.list

# Add repository key
curl -L https://couchdb.apache.org/repo/bintray-pubkey.asc | sudo apt-key add -

# Update and install
sudo apt-get update
sudo apt-get install couchdb
```

#### On macOS (using Homebrew):

```bash
brew install couchdb
```

### Configuration

Edit the CouchDB configuration file (usually located at `/opt/couchdb/etc/local.ini`):

```ini
[couchdb]
single_node=true

[chttpd]
port=5984
bind_address=0.0.0.0

[couch_httpd_auth]
require_valid_user=true

[admins]
admin = -pbkdf2-847043df5c7598b97bc2b0f4444fa7455980ec71,8b307a2639c2bf8c,10
```

Start CouchDB:

```bash
sudo systemctl start couchdb
```

## 3. Basic CRUD Operations

### Create a Database

```bash
curl -X PUT http://admin:password@localhost:5984/mydb
```

### Create a Document

```bash
curl -X POST http://admin:password@localhost:5984/mydb -H "Content-Type: application/json" -d '{"name": "John Doe", "age": 30, "city": "New York"}'
```

### Read a Document

```bash
curl http://admin:password@localhost:5984/mydb/document_id
```

### Update a Document

```bash
curl -X PUT http://admin:password@localhost:5984/mydb/document_id -H "Content-Type: application/json" -d '{"_rev": "current_revision", "name": "John Doe", "age": 31, "city": "Los Angeles"}'
```

### Delete a Document

```bash
curl -X DELETE http://admin:password@localhost:5984/mydb/document_id?rev=current_revision
```

## 4. Mango Queries

Mango is CouchDB's declarative query language that allows you to find documents without using MapReduce views.

### Basic Mango Query

```bash
curl -X POST http://admin:password@localhost:5984/mydb/_find -H "Content-Type: application/json" -d '{
  "selector": {
    "age": {"$gt": 25}
  },
  "fields": ["name", "age"],
  "sort": [{"age": "asc"}],
  "limit": 10
}'
```

### Mango Query Operators

1. Comparison Operators

   - `$eq`: Equal to
   - `$gt`: Greater than
   - `$gte`: Greater than or equal to
   - `$lt`: Less than
   - `$lte`: Less than or equal to
   - `$ne`: Not equal to

   Example:
   ```json
   {
     "selector": {
       "age": {"$gte": 18, "$lte": 30}
     }
   }
   ```

2. Logical Operators

   - `$and`: Logical AND
   - `$or`: Logical OR
   - `$not`: Logical NOT

   Example:
   ```json
   {
     "selector": {
       "$or": [
         {"age": {"$lt": 18}},
         {"age": {"$gt": 60}}
       ]
     }
   }
   ```

3. Array Operators

   - `$in`: In array
   - `$nin`: Not in array
   - `$all`: Contains all

   Example:
   ```json
   {
     "selector": {
       "tags": {"$all": ["javascript", "database"]}
     }
   }
   ```

4. Element Operators

   - `$exists`: Field exists
   - `$type`: Field is of specified type

   Example:
   ```json
   {
     "selector": {
       "middleName": {"$exists": false},
       "age": {"$type": "number"}
     }
   }
   ```

5. Regex Operator

   - `$regex`: Regular expression match

   Example:
   ```json
   {
     "selector": {
       "name": {"$regex": "^J"}
     }
   }
   ```

### Complex Mango Query Example

```bash
curl -X POST http://admin:password@localhost:5984/mydb/_find -H "Content-Type: application/json" -d '{
  "selector": {
    "$and": [
      {"age": {"$gte": 18, "$lte": 65}},
      {"$or": [
        {"city": {"$in": ["New York", "Los Angeles", "Chicago"]}},
        {"tags": {"$all": ["developer", "javascript"]}}
      ]},
      {"status": {"$ne": "inactive"}}
    ]
  },
  "fields": ["name", "age", "city", "tags"],
  "sort": [{"age": "asc"}, {"name": "asc"}],
  "limit": 20,
  "skip": 0,
  "execution_stats": true
}'
```

This query finds all active users between 18 and 65 years old, who either live in specific cities or have certain tags, sorted by age and name.

## 5. View Design Documents

Views in CouchDB use MapReduce functions to create indices for efficient querying.

### Creating a View

```bash
curl -X PUT http://admin:password@localhost:5984/mydb/_design/users -H "Content-Type: application/json" -d '{
  "views": {
    "by_age": {
      "map": "function(doc) { if (doc.type === 'user' && doc.age) { emit(doc.age, doc); } }"
    },
    "count_by_city": {
      "map": "function(doc) { if (doc.type === 'user' && doc.city) { emit(doc.city, 1); } }",
      "reduce": "_count"
    },
    "avg_age_by_city": {
      "map": "function(doc) { if (doc.type === 'user' && doc.age && doc.city) { emit(doc.city, doc.age); } }",
      "reduce": "_stats"
    }
  }
}'
```

### Querying Views

1. Basic View Query

   ```bash
   curl http://admin:password@localhost:5984/mydb/_design/users/_view/by_age?startkey=20&endkey=30
   ```

2. Reduced View Query

   ```bash
   curl http://admin:password@localhost:5984/mydb/_design/users/_view/count_by_city?group=true
   ```

3. Complex View Query with Aggregation

   ```bash
   curl http://admin:password@localhost:5984/mydb/_design/users/_view/avg_age_by_city?group=true
   ```

### Built-in Reduce Functions

1. `_count`: Counts the number of emitted values
2. `_sum`: Sums the emitted values
3. `_stats`: Calculates statistical summary (sum, count, min, max, sum_of_squares)

### Custom Reduce Function Example

```javascript
{
  "views": {
    "total_order_value": {
      "map": "function(doc) { if (doc.type === 'order') { emit(doc.customer_id, doc.total); } }",
      "reduce": "function(keys, values, rereduce) { return sum(values); }"
    }
  }
}
```

## 6. Full Text Search

CouchDB supports full-text search through Apache Lucene integration.

### Installation and Configuration

1. Install the search plugin:

   ```bash
   sudo npm install -g couchdb-fauxton-search-plugin
   ```

2. Add the following to your CouchDB configuration file:

   ```ini
   [httpd_global_handlers]
   _fti = {couch_httpd_proxy, handle_proxy_req, <<"http://127.0.0.1:5985">>}

   [fti]
   fti_index_dir = /path/to/lucene/indexes
   ```

3. Restart CouchDB

### Creating a Search Index

```bash
curl -X PUT http://admin:password@localhost:5984/mydb/_design/search -H "Content-Type: application/json" -d '{
  "fulltext": {
    "by_content": {
      "index": "function(doc) { if (doc.type === 'article' && doc.content) { var ret = new Document(); ret.add(doc.content); return ret; } }"
    }
  }
}'
```

### Querying the Search Index

```bash
curl http://admin:password@localhost:5984/mydb/_design/search/_search/by_content?q=couchdb
```

### Advanced Search Query

```bash
curl -X POST http://admin:password@localhost:5984/mydb/_design/search/_search/by_content -H "Content-Type: application/json" -d '{
  "q": "couchdb AND database",
  "include_docs": true,
  "highlight_fields": ["content"],
  "highlight_pre_tag": "<em>",
  "highlight_post_tag": "</em>",
  "limit": 10,
  "sort": ["-_score"]
}'
```

This query searches for documents containing both "couchdb" and "database", includes the full documents, highlights the matched terms, and sorts by relevance score.

## 7. Validation Documents

Validation documents allow you to enforce rules on document updates.

### Creating a Validation Document

```bash
curl -X PUT http://admin:password@localhost:5984/mydb/_design/validation -H "Content-Type: application/json" -d '{
  "validate_doc_update": "function(newDoc, oldDoc, userCtx, secObj) { if (newDoc.type === 'user') { if (!newDoc.name) { throw({forbidden: 'User must have a name'}); } if (typeof newDoc.age !== 'number' || newDoc.age < 0) { throw({forbidden: 'Age must be a positive number'}); } } }"
}'
```

This validation function ensures that user documents have a name and a valid age.

### Testing the Validation

Try to insert an invalid document:

```bash
curl -X POST http://admin:password@localhost:5984/mydb -H "Content-Type: application/json" -d '{
  "type": "user",
  "age": -5
}'
```

This should return a 403 Forbidden error.

## 8. Faceted Search

Faceted search allows for dynamic categorization of search results. While CouchDB doesn't have built-in faceted search, you can implement it using views or Mango queries.

### Implementing Facets with Views

1. Create a view for facets:

   ```bash
   curl -X PUT http://admin:password@localhost:5984/mydb/_design/facets -H "Content-Type: application/json" -d '{
     "views": {
       "by_category_and_tag": {
         "map": "function(doc) { if (doc.type === 'product') { emit([doc.category, doc.tags], 1); } }",
         "reduce": "_count"
       }
     }
   }'
   ```

2. Query the facets:

   ```bash
   curl http://admin:password@localhost:5984/mydb/_design/facets/_view/by_category_and_tag?group_level=1
   ```

   This will return counts for each category.

### Implementing Facets with Mango Queries

1. Create an index for faceted search:

   ```bash
   curl -X POST http://admin:password@localhost:5984/mydb/_index -H "Content-Type: application/json" -d '{
     "index": {
       "fields": ["type", "category", "tags"]
     },
     "name": "facet-index"
   }'
   ```

2. Perform a faceted search:

   ```bash
   curl -X POST http://admin:password@localhost:5984/mydb/_find -H "Content-Type: application/json" -d '{
     "selector": {
       "type": "product"
     },
     "fields": ["category", "tags"],
     "limit": 0
   }'
   ```

   You'll need to process the results on the client-side to count occurrences of each category and tag.

## 9. Replication and Sync

CouchDB's replication feature allows you to synchronize databases across multiple instances.

### Setting up One-Time Replication

```bash
curl -X POST http://admin:password@localhost:5984/_replicate -H "Content-Type: application/json" -d '{
  "source": "http://admin:password@localhost:5984/source_db",
  "target": 
 "http://admin:password@remotehost:5984/target_db"
}'
```

### Setting up Continuous Replication

```bash
curl -X POST http://admin:password@localhost:5984/_replicate -H "Content-Type: application/json" -d '{
  "source": "http://admin:password@localhost:5984/source_db",
  "target": "http://admin:password@remotehost:5984/target_db",
  "continuous": true
}'
```

### Monitoring Replication

```bash
curl http://admin:password@localhost:5984/_active_tasks
```

### Conflict Resolution

CouchDB doesn't automatically resolve conflicts. Instead, it keeps all conflicting versions and lets the application decide how to resolve them.

Example of resolving a conflict:

```javascript
async function resolveConflict(db, docId) {
  // Fetch the document with conflicts
  let doc = await db.get(docId, {conflicts: true});
  
  if (doc._conflicts) {
    for (let rev of doc._conflicts) {
      // Fetch each conflicting revision
      let conflictingDoc = await db.get(docId, {rev: rev});
      
      // Implement your merge strategy here
      // For this example, we'll just keep the most recent version
      if (conflictingDoc._rev > doc._rev) {
        doc = conflictingDoc;
      }
      
      // Delete the conflicting revision
      await db.remove(docId, rev);
    }
    
    // Save the resolved document
    await db.put(doc);
  }
}
```

## 10. Security and Authentication

CouchDB provides various security features to protect your data.

### Creating an Admin User

```bash
curl -X PUT http://localhost:5984/_config/admins/admin_username -d '"password"'
```

### Setting up Database-Level Security

```bash
curl -X PUT http://admin:password@localhost:5984/mydb/_security -H "Content-Type: application/json" -d '{
  "admins": {
    "names": ["admin1"],
    "roles": ["admin_role"]
  },
  "members": {
    "names": ["user1", "user2"],
    "roles": ["user_role"]
  }
}'
```

### Creating a User

```bash
curl -X PUT http://admin:password@localhost:5984/_users/org.couchdb.user:username -H "Content-Type: application/json" -d '{
  "name": "username",
  "password": "password",
  "roles": [],
  "type": "user"
}'
```

### Authenticating Requests

```bash
curl -X POST http://localhost:5984/_session -H "Content-Type: application/json" -d '{
  "name": "username",
  "password": "password"
}'
```

This will return a session cookie that can be used for subsequent requests.

## 11. Performance Tuning and Best Practices

1. **Use Bulk Operations**: For inserting or updating multiple documents, use bulk operations to reduce HTTP overhead.

   ```javascript
   const docs = [
     { _id: 'doc1', type: 'user', name: 'Alice' },
     { _id: 'doc2', type: 'user', name: 'Bob' }
   ];
   db.bulkDocs(docs).then(response => {
     console.log('Documents inserted:', response);
   }).catch(err => {
     console.error('Error inserting documents:', err);
   });
   ```

2. **Proper Indexing**: Create appropriate indexes for your most common queries.

   ```bash
   curl -X POST http://admin:password@localhost:5984/mydb/_index -H "Content-Type: application/json" -d '{
     "index": {
       "fields": ["type", "created_at"]
     },
     "name": "type-date-index"
   }'
   ```

3. **Use Partitioned Databases**: For large datasets, consider using partitioned databases to improve query performance.

   Creating a partitioned database:
   ```bash
   curl -X PUT http://admin:password@localhost:5984/mypartitioneddb?partitioned=true
   ```

   Inserting a document into a partition:
   ```bash
   curl -X PUT http://admin:password@localhost:5984/mypartitioneddb/partition1:doc1 -H "Content-Type: application/json" -d '{
     "_id": "partition1:doc1",
     "type": "user",
     "name": "Alice"
   }'
   ```

4. **Regular Compaction**: Set up regular compaction to keep your database size in check.

   ```bash
   curl -X POST http://admin:password@localhost:5984/mydb/_compact
   ```

5. **Use Caching**: Implement caching mechanisms to reduce database load for frequently accessed data.

6. **Optimize View Queries**: Use `startkey` and `endkey` for range queries, and `limit` to restrict the number of returned rows.

   ```bash
   curl http://admin:password@localhost:5984/mydb/_design/users/_view/by_age?startkey=20&endkey=30&limit=10
   ```

7. **Monitor and Tune**: Regularly monitor your CouchDB instance and tune configuration parameters as needed.

   Checking current configuration:
   ```bash
   curl http://admin:password@localhost:5984/_config
   ```

   Updating a configuration parameter:
   ```bash
   curl -X PUT http://admin:password@localhost:5984/_config/couchdb/max_dbs_open -d '"1000"'
   ```

By following these best practices and utilizing the advanced features of CouchDB, you can build scalable, efficient, and robust applications. Remember to always test your queries and optimizations with realistic datasets to ensure optimal performance in production environments.