## Installation

```sh
 npm install gstore-node --save
```

## Connect to the Google Cloud Node library

For all the information on how to configure the gcloud datastore, [read the docs here](https://googlecloudplatform.github.io/google-cloud-node/#/docs/datastore/master/datastore).

```js
const datastore = require('@google-cloud/datastore')();
const gstore = require('gstore-node');

gstore.connect(datastore);
```

## Aliases

After connecting gstore to the datastore, gstore has **2 aliases** set up

* `gstore.ds`  
The @google/datastore instance. With it you can access **all the API** of the library when needed.

* `gstore.transaction`. Alias of the same google-cloud/datastore method

