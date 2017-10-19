## Installation

```sh
 npm install gstore-node --save
```

## Connect to the Google Cloud Node library

For all the information on how to configure the gcloud datastore, [read the docs here](https://googlecloudplatform.github.io/google-cloud-node/#/docs/datastore/master/datastore).

```js
const datastore = require('@google-cloud/datastore')();
const gstore = require('gstore-node')();

gstore.connect(datastore);
```

## Aliases

After connecting gstore to the datastore, gstore has **2 aliases** set up

* `gstore.ds`  
  The @google/datastore instance. With it you can access **all the API** of the library when needed.

* `gstore.transaction`. Alias of the same google-cloud/datastore method

## Instances

You can create new instances of gstore by passing a configuration object. Each instance is identified by its namespace. If you don't specify a namespace it will get the default instance.

```js
// server.js
const settings1 = { namespace: 'namespace1' };
const datastore1 = require('@google-cloud/datastore')(settings1);
const gstore = require('gstore-node')(); // this is will be the default gstore instance
gstore.connect(datastore1);

const settings2 = { namespace: 'namespace2' };
const datastore2 = require('@google-cloud/datastore')(settings2);
const gstore2 = require('gstore-node')(settings2);
gstore2.connect(datastore2);


// anywhere in your models/controllers/...

const gstore = require('gstore-node')(); // Get default instance
// or
const gstore = require('gstore-node')({ namespace: 'namespace2' }); // Get the second instance


```



