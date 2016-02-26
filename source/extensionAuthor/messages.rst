.. Glimpse documentation master file, created by
   sphinx-quickstart on Fri Nov 20 16:04:45 2015.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

========
Messages
========

.. WARNING:: This site documents Glimpse |release|. It is a work in progress, and still has a long way to go.

   For Glimpse v1 documentation, please visit `getglimpse.com/Docs <http://getglimpse.com/Docs/>`_.


This document outlines the standard message formats that Glimpse uses to transport and store data.

.. TIP:: Messages are documented using `JSON Schema <http://json-schema.org/>`_. For the uninitiated, there's `a great open source JSON Schema book <http://spacetelescope.github.io/understanding-json-schema/>`_.

.. TIP:: Messages can be `loosely validated with online tooling <http://jsonschemalint.com/draft4/>`_. To author your own schema, be sure to check out `the online generator <http://jsonschema.net/>`_.

.. There's [a very nice schema visualizer](http://jlblcc.github.io/json-schema-viewer/) that would make the docs easy to read, but we have to figure out how to use it first.

Logging
=======

Write
------
**Type:** ``log-write``

**Schema:** (`Link <http://schema.getglimpse.com/log/write>`_)

.. include:: \..\messages\log\write.json
   :code: json

.. NOTE:: The ``pattern`` property should be a JavaScript compatible `Regular Expression <https://en.wikipedia.org/wiki/Regular_expression>`_ representation of log messages that leverage `string interpolation <https://en.wikipedia.org/wiki/String_interpolation>`_. The purpose of this property is allow Glimpse clients to treat interpolated values specially, `as demonstrated on JSFiddle <https://jsfiddle.net/coc2yofo/12/>`_. 

  Regular Expressions were chosen since they are `nearly universal <https://en.wikipedia.org/wiki/Comparison_of_regular_expression_engines#Language_features>`_ and can represent any interpolation format. (``%s``, ``{0}``, ``{1:d}``, ``{foo}``, ``$(bar)``, etc.)

MongoDB
=======

Insert
------

**Type:** ``data-mongodb-insert``

**Schema:** (`Link <http://schema.getglimpse.com/data/mongodb/insert>`_)

.. include:: \..\messages\data\mongodb\insert.json
   :code: json


Read
----

Reads tend to be more complex than writes because of cursors.

This annotated time line shows the approach we'll take to measure duration, for now. ::

                    + <--+ Connection Opened
                    |
                    |
               +--> | <--+ Command Issued
               |    |
 Duration NOT  |    |
 Measured +--> |    |
 by Glimpse    |    |
 (for now)     |    |
               +--> | <--+ Cursor Returned (cursor not yet opened).
                    |
                    | <--+
                    |    |
                    |    |
                    |    | <--+ Interact w/ Cursor (e.g., set projection, limit, sort-order,...)
                    |    |
                    |    |
                    | <--+
                    |
               +--> | <--+ First "data read" operation on cursor (cursor will be opened).
               |    |
  Duration     |    |
 Measured +--> |    |
 by Glimpse    |    |
 (for now)     |    |
               +--> | <--+ Result returned
                    |
                    |
               +--> | <--+ Subsequent "data read" operations on cursor
               |    |
 Duration NOT  |    |
 Measured +--> |    |
 by Glimpse    |    |
 (for now)     |    |
               +--> | <--+ Result Returned
                    |
                    | <--+ Cursor Closed
                    |
                    |
                    + <--+ Connection Closed


**Instrumented Collection Operations**
 - count()
 - findOne() - In Glimpse for Node.js, messages for findOne() will have an operation field with a value of "next".  This is because the findOne() implementation ultimately calls Cursor's next() method.

**Instrumented Cursor Operations**
 - toArray() - time measured by Glimpse is the time to return all of the results to the client.
 - forEach() - time measured by Glimpse is the time to return the first result.  Only the first call to forEach() will have a Glimpse message associated with it.  
 - next() - Same behavior as forEach().  
 - each() - Same behavior as forEach().  
 - nextObject() - Same behavior as forEach().  


**Type:** ``data-mongodb-read``

**Schema:** (`Link <http://schema.getglimpse.com/data/mongodb/read>`_)

.. include:: \..\messages\data\mongodb\read.json
   :code: json

.. NOTE:: The ``options`` may need to be cobbled together based on `these possible calls <http://mongodb.github.io/node-mongodb-native/2.0/reference/crud/#read-methods>`_ on the cursor itself.

Update
------

**Type:** ``data-mongodb-update``

**Schema:** (`Link <http://schema.getglimpse.com/data/mongodb/update>`_)

.. include:: \..\messages\data\mongodb\update.json
   :code: json

Delete
------

**Type:** ``data-mongodb-delete``

**Schema:** (`Link <http://schema.getglimpse.com/data/mongodb/delete>`_)

.. include:: \..\messages\data\mongodb\delete.json
   :code: json
   
Undocumented Methods
--------------------

The `MongoDB Collection Driver API <http://mongodb.github.io/node-mongodb-native/2.0/api/>`_ has *lots* of methods. The above schemas only cover a subset of them. The following methods are yet to be covered:

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