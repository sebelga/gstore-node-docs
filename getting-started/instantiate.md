# Create a gstore instance

## Instantiate

In order to get a gstore instance, import the module and then call it and provide an optional configuration object. The gstore instances are cached so you can do this in multiple places of your application, **the same instance will always be returned**.

```javascript
const GstoreNode = require('gstore-node');
const gstore = GstoreNode(/*optional*/<config>);
```

## Configuration

Optionally you can provide a configuration object when creating the instance with the following properties:

### namespace \(string\)

String \(`default: "gstore-node"`\)

This allows you to create multiple instances of gstore. The following example will create 2 instances of gstore that can connect to 2 different Datastore client instances \(see below\).

```javascript
const GstoreNode = require('gstore-node');
const gstoreDefaultInstance = GstoreNode();
const gstoreInstance1 = GstoreNode({ namespace: 'instance-1' });
const gstoreInstance2 = GstoreNode({ namespace: 'instance-2' });

// anywhere in your application...

const gstoreDefaultInstance = require('gstore-node')(); // Get the default instance
// or
const gstoreInstance1 = require('gstore-node')({ namespace: 'instance-1' }); // Get other instance
```

### errorOnEntityNotFound

Boolean \(`default: true`\)

By default if you fetch an entity by Key and the entity is not found in the Datastore, gstore will throw an "_ERR\_ENTITY\_NOT\_FOUND_" error. If you prefer to have `null` returned when an entity is not found, set `errorOnEntityNotFound` to `false`.

```text
const GstoreNode = require('gstore-node');
const gstore = GstoreNode({ errorOnEntityNotFound: false });
```

### cache

Refer to [the cache documentation](../cache-dataloader/cache.md) for a detail explanation of the cache configuration.

## Connect to the Google Cloud Node library

For all the information on how to configure the Goolge Datastore client, [read the docs here](https://cloud.google.com/nodejs/docs/reference/datastore/2.0.x/Datastore).

```javascript
const datastore = require('@google-cloud/datastore')();
const GstoreNode = require('gstore-node');
const gstore = GstoreNode();

gstore.connect(datastore);

// Create another gstore instance and connect it to another Datastore instance (with a different namespace)
const namespace = 'another-namespace';
const datastore2 = require('@google-cloud/datastore')({ namespace });
const gstore2 = GstoreNode({ namespace });
```

## Aliases

After connecting gstore to the datastore, gstore has **2 aliases** set up

* `gstore.ds` The underlying @google/datastore instance. This allows you to access **the full API** of the Datastore client whenever needed.
* `gstore.transaction`. Alias of the same google-cloud/datastore method

