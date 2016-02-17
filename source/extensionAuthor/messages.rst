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
     Duration      |    |
     Measured +--> |    |
    by Glimpse     |    |
    (for now)      |    |
                   +--> | <--+ Cursor Opened
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