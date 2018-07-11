# Motivation

The Google Datastore is an amazingly fast, reliable and flexible database for today's modern applications. But its flexibility and schemaless nature mean that, unless the Datastore is used to save raw data, it is the developer's responsibility to sanitize and validate the properties saved for each entity. This can quickly become a tedious task and this is where a modeling library like gstore-node with its schemas help to ensure the expected values are stored and retrieved for each entity property.

The pre & post 'hooks' found in Mongoose are also of great help when it comes to working with denormalized data on a NoSQL database.

As gstore-node is built on top of the @google-cloud-node library, **all of its API** can still be accessed whenever needed through the alias `gstore.ds`

