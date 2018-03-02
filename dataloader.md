# Dataloader

[Dataloader](https://github.com/facebook/dataloader) is a utility from Facebook to "reduce requests to the backends via batching and caching".

It combines all individuals loads within a single frame of execution (a single tick of the event loop) and then calls its batch function with all requested keys, making sure we don't hit the backend asking for the same key twice.

Although Dataloader has a memoization cache for all loads which occur in a single request, it is not meant to be used as a cache layer. As it excplicitly says, Dataloader "is a data loading mechanism, and its cache only serves the purpose of not repeatedly loading the same data in the context of a single request to your Application".

### How it works

First make sure that you have dataloader installed.

```sh
npm install --save dataloader
```

#### gstore.createDataLoader()

createDataLoader() is a **helper** to create a Dataloader instance with a batch function that works with the Google Datastore.
You will need to create a new Dataloader instance **for each request**, and pass it to gstore methods as an option property. 

Let's see it with some examples.

```js
// user.routes.js

const gstore = require('gstore-node');
const User = require('../users/users.model');

const { createDataLoader } = gstore;

const getUser = (req, res) => {
    const dataloader = createDataLoader();
    User.get(req.params.id, null, null, null, { dataloader })
        .then((entity) => { ... });
};

```

If you only had this piece of code then the Dataloader would not be of much help. Let's see with a more complexe data fetching

```js
// image.js

const Image = require('./image.model');

const getImage = (id, dataloader) => {

};

module.exports = { getImage };

```