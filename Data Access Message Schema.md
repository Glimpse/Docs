#Data Access

This document outlines the standard message formats that Glimpse uses to 

_**Note:** _ It may behoove us to document these messages in [JSON Schema](http://json-schema.org/). If we do, we have the benefit of being able to [loosely validate messages](http://jsonschemalint.com/draft4/), and the authoring process could be [accelerated with a generator](http://jsonschema.net/). There's [a very nice schema visualizer](http://jlblcc.github.io/json-schema-viewer/) that would make the docs easy to read, but we have to figure out how to use it first.

For the uninitiated, there's [a great open source JSON Schema book](http://spacetelescope.github.io/understanding-json-schema/). It's also available as a [PDF](http://spacetelescope.github.io/understanding-json-schema/UnderstandingJSONSchema.pdf).


## General

> This section is a WIP and should be ignored until further notice.

These properties should appear on all standard data access messages.

```json
{
	duration: 321,
	connection: 'my connection string/server identifier'
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
> This section is a WIP and should be ignored until further notice.

Considerations:
- Cursor Settings, there's [a lot of them](http://mongodb.github.io/node-mongodb-native/2.0/reference/crud/#read-methods) and they are chainable.

Fields
- method call (find | aggregate)

### Insert

> This section is a WIP and should be ignored until further notice.
 
Has a:
- method call (insertOne vs insertMany?)
- isMajority (bool based on the w parameter)
- timeout
- waitForJournalSync (bool)

### Update

> This section is a WIP and should be ignored until further notice.

Has a:
- selector
- update command
- method call (updateOne | updateMany | findOneAndUpdate | findOneAndReplace)
- isMajority (bool based on the w parameter)
- timeout
- waitForJournalSync (bool)
- isMulti (bool update more than one doc at a time)
- isUpsert (bool is this an upsert)

- sort, projection & returnOriginal for findOneAndUpdate

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
			"enum": ["deleteOne", "deleteMany", "findOneAndDelete"]
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
		}
		"collection": {
			"id": "http://getglimpse.com/collection",
			"type": "string",
			"title": "Collection",
			"description": "The MongoDb collection that was operated against."
		}
	},
	"required": ["operation", "query", "count", "duration", "connectionPort", "connectionHost", "collection"]
}
```

**Open Questions:**
- Mongo has a "success flag". I'm not sure if we should put a success flag on these messages, or instead have "error" messages.

### Connection Open

> This section is a WIP and should be ignored until further notice.

The most common connection options can be passed accross on the server URI. There are additional options however that are listed in [Mongo's Connection Settings doc](http://mongodb.github.io/node-mongodb-native/2.0/reference/connecting/connection-settings/).

### Connection Close

### Bulk Command

> This section is a WIP and should be ignored until further notice.

- there is a list of insert, update and delete commands