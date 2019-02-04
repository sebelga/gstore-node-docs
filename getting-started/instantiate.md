# Create a gstore instance

## Instantiate

In order to get a gstore instance, import the module and then call it and provide an optional configuration object. The gstore instances are cached so you can do this in multiple places of your application, **the same instance will always be returned**.

```javascript
const { Gstore } = require('gstore-node');
const gstore = new Gstore(/*optional*/<config>);
```

## Configuration

Optionally you can provide a configuration object when creating the instance with the following properties:

### cache

Refer to [the cache documentation](../cache-dataloader/cache.md) for a detail explanation of the cache configuration.

```javascript
const { Gstore } = require('gstore-node');
const gstore = new Gstore({ cache: true });
```

### errorOnEntityNotFound

Boolean \(`default: true`\)

By default if you fetch an entity by Key and the entity is not found in the Datastore, gstore will throw an "_ERR\_ENTITY\_NOT\_FOUND_" error. If you prefer to have `null` returned when an entity is not found, set `errorOnEntityNotFound` to `false`.

```javascript
const { Gstore } = require('gstore-node');
const gstore = new Gstore({ errorOnEntityNotFound: false });
```

## Connect to the Google Cloud Node library

For all the information on how to configure the Goolge Datastore client, [read the docs here](https://cloud.google.com/nodejs/docs/reference/datastore/2.0.x/Datastore).

```javascript
const { Datastore } = require('@google-cloud/datastore');
const { Gstore } = require('gstore-node');

const gstore = new Gstore();
const datastore = new Datastore();

gstore.connect(datastore);
```

## Aliases

After connecting gstore to the datastore, the gstore instance has **2 aliases** set up

* `gstore.ds` The underlying @google/datastore instance. This allows you to access **the full API** of the Datastore client whenever needed.
* `gstore.transaction`. Alias of the same google-cloud/datastore method

## Instances

Each time you call `const gstore = new Gstore()` you create a **new** instance. This means that you are responsible to cache that instance to be able to retrieve it in different places of your application. One way to do that is to initialize gstore in a separate file and require the instance from there.

```javascript
// db.js
const { Gstore } = require('gstore-node');
const gstore = new Gstore();

module.exports = { gstore };
```

```javascript
// You can then access the same instance anywhere in your application with
const { gstore } = require('./db');
```

Another way is to use gstore `instances` to **save** and **retrieve** gstore instances.

```javascript
// server.js
const { Gstore, instances } = require('gstore-node');
const { Datastore } = require('@google-cloud/datastore');

const gstore1 = new Gstore();
const gstore2 = new Gstore({ cache: true }); // instance with cache turned "on"
const gstore3 = new Gstore(); // instance that we will connect to a different Datastore "namespace"

const datastore1 = new Datastore({ namespace: 'dev' });
const datastore2 = new Datastore({ namespace: 'staging' });

gstore1.connect(datastore1);
gstore2.connect(datastore1);
gstore3.connect(datastore2); // different namespace

// Give a unique ID to each instance
instances.set('default', gstore1);
instances.set('cache-on', gstore2);
instances.set('staging', gstore3);

...

// Anywhere in your application...
const { instances } = require('gstore-node');

const gstoreDefaultInstance = instances.get('default');
// or
const gstoreWithCache = instances.get('cache-on');
// or
const gstoreStaging = instances.get('staging');
```

