# Motivation

The Google Datastore is an amazing fast, reliable and flexible database for today's modern applications. But it's flexibility and schemaless nature can sometimes lead to a lot of duplicate code if we need to validate the entities to be saved.  

The pre & post 'hooks' found in Mongoose are also of great value when it comes to work with entities on a NoSQL database.

As it is built on top of the @google-cloud-node library, **all of its API** can still be accessed whenever needed through the alias `gstore.ds`