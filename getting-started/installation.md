# Installation

## Npm dependency

```bash
 npm install gstore-node --save
 # or
 yarn add gstore-node
```

## Connect to the Google Cloud Node library

For all the information on how to configure the gcloud datastore, [read the docs here](https://cloud.google.com/nodejs/docs/reference/datastore/1.4.x/Datastore).

```javascript
const datastore = require('@google-cloud/datastore')();
const gstore = require('gstore-node')();

gstore.connect(datastore);
```

## Aliases

After connecting gstore to the datastore, gstore has **2 aliases** set up

* `gstore.ds` The @google/datastore instance. With it you can access **all the API** of the library when needed.
* `gstore.transaction`. Alias of the same google-cloud/datastore method

## Instances

You can create new instances of gstore by passing a configuration object. Each instance is identified by its namespace. If you don't specify a namespace it will get the default instance.

```javascript
// server.js
const datastore1 = require('@google-cloud/datastore')({ namespace: 'namespace1' });
const gstore1 = require('gstore-node')(); // this is will be the default gstore instance
gstore1.connect(datastore1);

const datastore2 = require('@google-cloud/datastore')({ namespace: 'namespace2' });
const gstore2 = require('gstore-node')({ namespace: 'namespace2' });
gstore2.connect(datastore2);


// anywhere in your application...

const gstore = require('gstore-node')(); // Get default instance
// or
const gstore = require('gstore-node')({ namespace: 'namespace2' }); // Get the second instance
```

