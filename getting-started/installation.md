## Installation
 ```sh
 npm install gstore-node --save
 ```

### Connect to the Google Cloud Node lib
For info on how to configure the gcloud datastore [read the docs here](https://googlecloudplatform.github.io/google-cloud-node/#/docs/datastore/0.5.0/datastore).

```js
var datastore = require('@google-cloud/datastore')();
var gstore = require('gstore-node');

gstore.connect(datastore);
```

#### Aliases

After a successful connection, gstore has 2 aliases set up

- `gstore.ds` The gcloud datastore instance
- `gstore.transaction`. Alias of the same gcloud method
