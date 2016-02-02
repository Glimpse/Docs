#Data Access

This document outlines the standard message formats that Glimpse uses to 

_**Note:** _ It may behoove us to document these messages in [JSON Schema](http://json-schema.org/). If we do, we have the benefit of being able to [loosely validate messages](http://jsonschemalint.com/draft4/), and the authoring process could be [accelerated with a generator](http://jsonschema.net/). There's [a very nice schema visualizer](http://jlblcc.github.io/json-schema-viewer/) that would make the docs easy to read, but we have to figure out how to use it first.

For the uninitiated, there's [a great open source JSON Schema book](http://spacetelescope.github.io/understanding-json-schema/). It's also available as a [PDF](http://spacetelescope.github.io/understanding-json-schema/UnderstandingJSONSchema.pdf).


## General

> This section is a WIP and should be ignored until further notice.

These properties should appear on all standard data access messages.

```json
{
	"duration": 321,
	"connection": "my connection string/server identifier"
}
```

### Read

### Insert

### Update

### Delete

### Non-Standard Operations
- Database Connection Open/Close
- Transaction Begin/End

## MongoDB Specific

MongoDB's driver supports both callback and promises based API's. Both should be instrumented.


### Read
Reads tend to be more complex than writes because of cursors.

This annotated time line shows the approach we'll take to measure duration, for now.
```
                    + <--+ Connection Opened
                    |
                    |
               +--> | <--+ Command Issued
               |    |
 Duration      |    |
 Measured +--> |    |
by Glimpse     |    |
(for now)      |    |
               +--> | <--+ Cursor Returned
                    |
                    | <--+
                    |    |
                    |    |
                    |    | <--+ Interact w/ Cursor
                    |    |
                    |    |
                    | <--+
                    |
                    | <--+ Cursor Closed
                    |
                    |
                    + <--+ Connection Closed

```

**Type Name:** `mongo-find`

**Schema:**

_Note: The `options` may need to be cobbled together based on these possible calls on the cursor itself:_


```json
{
	"$schema": "http://json-schema.org/draft-04/schema#",
	"id": "http://getglimpse.com/message-schema/mongo-find.json",
	"type": "object",
	"title": "MongoDb Find Operation",
	"description": "Schema for find operations against a MongoDb database.",
	"properties": {
		"operation": {
			"id": "http://getglimpse.com/operation",
			"type": "string",
			"title": "Update Operation",
			"description": "The driver method that was called to execute an update.",
			"enum": ["find", "findOne", "count"]
		},
		"query": {
			"id": "http://getglimpse.com/query",
			"type": "string",
			"title": "Query",
			"description": "The query/filter that was used to execute the find."
		},
		"startTime": {
			"id": "http://getglimpse.com/startTime",
			"type": "string",
			"format": "date-time",
			"title": "Start Time",
			"description": "The time the operation began in the format specified in RFC3339 Section 5.6."
		},
		"duration": {
			"id": "http://getglimpse.com/duration",
			"type": "number",
			"minimum": 0,
			"title": "Duration",
			"description": "The amount of time, in milliseconds, that the operation took to execute."
		},
		"options": {
			"id": "http://getglimpse.com/options",
			"type": ["object", "null"],
			"title": "Options",
			"description": "Any additional options that were passed into the operation. May be null."
		},
		"connectionPort": {
			"id": "http://getglimpse.com/connection-port",
			"type": "integer",
			"minimum": 0,
			"maximum": 65535,
			"title": "Connection Port",
			"description": "The TCP/IP port used to connect to the MongoDb server."
		},
		"connectionHost": {
			"id": "http://getglimpse.com/connection-host",
			"type": "string",
			"title": "Connection Host",
			"description": "The host of the MongoDb server."
		},
		"database": {
			"id": "http://getglimpse.com/database",
			"type": "string",
			"title": "Database",
			"description": "The MongoDb database that was operated against."
		},
		"collection": {
			"id": "http://getglimpse.com/collection",
			"type": "string",
			"title": "Collection",
			"description": "The MongoDb collection that was operated against."
		}
	},
	"required": ["operation", "query", "startTime", "duration", "connectionPort", "connectionHost", "collection"]
}
```

Considerations:
- Cursor Settings, there's [a lot of them](http://mongodb.github.io/node-mongodb-native/2.0/reference/crud/#read-methods) and they are chainable.

Fields
- method call (find | aggregate)

### Insert

**Type Name:** `mongo-insert`

**Schema:**
```json
{
	"$schema": "http://json-schema.org/draft-04/schema#",
	"id": "http://getglimpse.com/message-schema/mongo-insert.json",
	"type": "object",
	"title": "MongoDb Insert Operation",
	"description": "Schema for insert operations against a MongoDb database.",
	"properties": {
		"operation": {
			"id": "http://getglimpse.com/operation",
			"type": "string",
			"title": "Insert Operation",
			"description": "The driver method that was called to execute an insert.",
			"enum": ["insert", "insertOne", "insertMany"]
		},
		"docs": {
			"id": "http://getglimpse.com/docs",
			"type": "array",
			"minItems": 1,
			"title": "Documents to Insert",
			"description": "The document(s) to insert."
		},
		"count": {
			"id": "http://getglimpse.com/count",
			"type": "integer",
			"multipleOf": 1,
			"minimum": 0,
			"title": "Record Count",
			"description": "The number of documents that were inserted in this operation."
		},
		"insertedIds": {
			"id": "http://getglimpse.com/inserted",
			"type": "array",
			"minItems": 1,
			"title": "Inserted Document Ids",
			"description": "The Id's of the inserted document(s)."
		},
		"startTime": {
			"id": "http://getglimpse.com/startTime",
			"type": "string",
			"format": "date-time",
			"title": "Start Time",
			"description": "The time the operation began in the format specified in RFC3339 Section 5.6."
		},
		"duration": {
			"id": "http://getglimpse.com/duration",
			"type": "number",
			"minimum": 0,
			"title": "Duration",
			"description": "The amount of time, in milliseconds, that the operation took to execute."
		},
		"options": {
			"id": "http://getglimpse.com/options",
			"type": ["object", "null"],
			"title": "Options",
			"description": "Any additional options that were passed into the operation. May be null."
		},
		"connectionPort": {
			"id": "http://getglimpse.com/connection-port",
			"type": "integer",
			"minimum": 0,
			"maximum": 65535,
			"title": "Connection Port",
			"description": "The TCP/IP port used to connect to the MongoDb server."
		},
		"connectionHost": {
			"id": "http://getglimpse.com/connection-host",
			"type": "string",
			"title": "Connection Host",
			"description": "The host of the MongoDb server."
		},
		"database": {
			"id": "http://getglimpse.com/database",
			"type": "string",
			"title": "Database",
			"description": "The MongoDb database that was operated against."
		},
		"collection": {
			"id": "http://getglimpse.com/collection",
			"type": "string",
			"title": "Collection",
			"description": "The MongoDb collection that was operated against."
		}
	},
	"required": ["operation", "docs", "insertedIds", "count", "startTime", "duration", "connectionPort", "connectionHost", "collection"]
}
```

### Update

**Type Name:** `mongo-update`

**Schema:** 
```json
{
	"$schema": "http://json-schema.org/draft-04/schema#",
	"id": "http://getglimpse.com/message-schema/mongo-update.json",
	"type": "object",
	"title": "MongoDb Update Operation",
	"description": "Schema for update operations against a MongoDb database.",
	"properties": {
		"operation": {
			"id": "http://getglimpse.com/operation",
			"type": "string",
			"title": "Update Operation",
			"description": "The driver method that was called to execute an update.",
			"enum": ["updateOne", "updateMany", "replaceOne", "findOneAndUpdate"]
		},
		"query": {
			"id": "http://getglimpse.com/query",
			"type": "string",
			"title": "Query",
			"description": "The query/filter that was used to execute the update."
		},
		"updates": {
			"id": "http://getglimpse.com/updates",
			"type": "object",
			"title": "Update Command",
			"description": "The update command."
		},
		"matchedCount": {
			"id": "http://getglimpse.com/matchedCount",
			"type": "integer",
			"multipleOf": 1,
			"minimum": 0,
			"title": "Matched Count",
			"description": "The number of documents that matched the query."
		},
		"modifiedCount": {
			"id": "http://getglimpse.com/modifiedCount",
			"type": "integer",
			"multipleOf": 1,
			"minimum": 0,
			"title": "Modified Count",
			"description": "The number of documents that were modified."
		},
		"upsertedCount": {
			"id": "http://getglimpse.com/upsertedCount",
			"type": "integer",
			"multipleOf": 1,
			"minimum": 0,
			"title": "Upserted Count",
			"description": "The number of documents that were upserted."
		},
		"startTime": {
			"id": "http://getglimpse.com/startTime",
			"type": "string",
			"format": "date-time",
			"title": "Start Time",
			"description": "The time the operation began in the format specified in RFC3339 Section 5.6."
		},
		"duration": {
			"id": "http://getglimpse.com/duration",
			"type": "number",
			"minimum": 0,
			"title": "Duration",
			"description": "The amount of time, in milliseconds, that the operation took to execute."
		},
		"options": {
			"id": "http://getglimpse.com/options",
			"type": ["object", "null"],
			"title": "Options",
			"description": "Any additional options that were passed into the operation. May be null."
		},
		"connectionPort": {
			"id": "http://getglimpse.com/connection-port",
			"type": "integer",
			"minimum": 0,
			"maximum": 65535,
			"title": "Connection Port",
			"description": "The TCP/IP port used to connect to the MongoDb server."
		},
		"connectionHost": {
			"id": "http://getglimpse.com/connection-host",
			"type": "string",
			"title": "Connection Host",
			"description": "The host of the MongoDb server."
		},
		"database": {
			"id": "http://getglimpse.com/database",
			"type": "string",
			"title": "Database",
			"description": "The MongoDb database that was operated against."
		},
		"collection": {
			"id": "http://getglimpse.com/collection",
			"type": "string",
			"title": "Collection",
			"description": "The MongoDb collection that was operated against."
		}
	},
	"required": ["operation", "query", "updates", "matchedCount", "modifiedCount", "upsertedCount", "startTime", "duration", "connectionPort", "connectionHost", "collection"]
}
```

### Delete

**Type Name:** `mongo-delete`

**Schema:** 
```json
{
	"$schema": "http://json-schema.org/draft-04/schema#",
	"id": "http://getglimpse.com/message-schema/mongo-delete.json",
	"type": "object",
	"title": "MongoDb Delete Operation",
	"description": "Schema for delete operations against a MongoDb database.",
	"properties": {
		"operation": {
			"id": "http://getglimpse.com/operation",
			"type": "string",
			"title": "Delete Operation",
			"description": "The driver method that was called to execute a delete.",
			"enum": ["deleteOne", "deleteMany", "findOneAndDelete", "remove"]
		},
		"query": {
			"id": "http://getglimpse.com/query",
			"type": "string",
			"title": "Query",
			"description": "The query/filter that was used to execute the delete."
		},
		"count": {
			"id": "http://getglimpse.com/count",
			"type": "integer",
			"multipleOf": 1,
			"minimum": 0,
			"title": "Record Count",
			"description": "The number of documents that were deleted in this operation."
		},
		"startTime": {
			"id": "http://getglimpse.com/startTime",
			"type": "string",
			"format": "date-time",
			"title": "Start Time",
			"description": "The time the operation began in the format specified in RFC3339 Section 5.6."
		},
		"duration": {
			"id": "http://getglimpse.com/duration",
			"type": "number",
			"minimum": 0,
			"title": "Duration",
			"description": "The amount of time, in milliseconds, that the operation took to execute."
		},
		"options": {
			"id": "http://getglimpse.com/options",
			"type": ["object", "null"],
			"title": "Options",
			"description": "Any additional options that were passed into the operation. May be null."
		},
		"connectionPort": {
			"id": "http://getglimpse.com/connection-port",
			"type": "integer",
			"minimum": 0,
			"maximum": 65535,
			"title": "Connection Port",
			"description": "The TCP/IP port used to connect to the MongoDb server."
		},
		"connectionHost": {
			"id": "http://getglimpse.com/connection-host",
			"type": "string",
			"title": "Connection Host",
			"description": "The host of the MongoDb server."
		},
		"database": {
			"id": "http://getglimpse.com/database",
			"type": "string",
			"title": "Database",
			"description": "The MongoDb database that was operated against."
		},
		"collection": {
			"id": "http://getglimpse.com/collection",
			"type": "string",
			"title": "Collection",
			"description": "The MongoDb collection that was operated against."
		}
	},
	"required": ["operation", "query", "count", "startTime", "duration", "connectionPort", "connectionHost", "database", "collection"]
}
```

### Undocumented Calls

These `collection` methods are not accounted for (yet) in this document.

- aggregate
- bulkWrite
- createIndex
- createIndexes
- distinct
- drop
- dropAllIndexes
- dropIndex
- dropIndexes
- ensureIndex
- findAndModify
- findAndRemove
- findOneAndReplace
- geoHaystackSearch
- geoNear
- group
- indexes
- indexExists
- indexInformation
- initializeOrderedBulkOp
- initializeUnorderedBulkOp
- isCapped
- listIndexes
- mapReduce
- options
- parallelCollectionScan
- reIndex
- rename
- save
- stats
- update


### Connection Open

> This section is a WIP and should be ignored until further notice.

The most common connection options can be passed accross on the server URI. There are additional options however that are listed in [Mongo's Connection Settings doc](http://mongodb.github.io/node-mongodb-native/2.0/reference/connecting/connection-settings/).

### Connection Close

### Bulk Command

> This section is a WIP and should be ignored until further notice.

- there is a list of insert, update and delete commands
