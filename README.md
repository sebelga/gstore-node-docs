# gstore Node.js

gstore-node is a Google Datastore entities modeling library for Node.js inspired by Mongoose and built on top of the google-cloud-node library.

Its main features are:

* explicit **Schema declaration** for entities
* properties **type validation**
* properties **value validation**
* **shortcuts** queries
* pre & post **middleware** (hooks)
* **custom methods** on entity instances


## Motivation

The Google Datastore is an amazing fast, reliable and flexible database for today's modern apps. But it's flexibility and schemaless nature can sometimes lead to a lot of duplicate code to validate the properties passed and their values. The pre & post 'hooks' found in Mongoose are also of great value when it comes to work with entities on a NoSQL database. As it is built on top of the great gcloud-node library, all of its API can still be accessed whenever needed.