# Model Methods

## get\(\)

Get one or multiple entities from the Datastore. Retrieving an entity **by key** is the fastest way to read from the Datastore.  
This method accepts the following arguments:

```js
MyModel.get(
    /* {int|string|Array<int|string>}. -- id(s) to retrieve */
    <id>,
    /* {Array} -- optional. ex: ['ParentEntity', 1234 ] */
    <ancestors>,
    /* {string} -- optional. A specific namespace */
    <namespace>,
    /* {Transaction} -- optional. The transaction currently in progress */
    <transaction>,
    /* {object} -- optional. Additional config */
    <options>,
    /* {function} -- optional. The callback, if not passed a Promise is returned */
    <callback>
)
```

**@Returns** -- a gstore entity **instance**.

---

Example:

```js
const BlogPost = require('./blog-post.model');

// id can be an integer or a string
BlogPost.get(123).then((entity) => {
    console.log(entity.plain());
});

// Array of ids
BlogPost.get([1,2,3]).then((entities) => {
    entities = entities.map(entity => entity.plain());
});

// Passing an ancestor path with a Kind and a name
const ancestors = ['Parent', 'parentName'];
BlogPost.get('stringId', ancestors).then((entity) => { ... });

// ... with a callback
BlogPost.get(1234, function onEntity(err, entity) {
    if (err) { // deal with err }
    console.log('Blogpost title:', entity.title);
});
```

The resulting entity has a **plain\(\)** method that returns an object with the entity **data ** + its **id**. See [the doc here](../entity/methods/plain.md).

```js
BlogPost.get(123).then(entity) {
    console.log(entity.plain());
});
```

If you need to retrieve an entity **from inside a transaction**, you can pass the transaction object as fourth parameter.

```js
const transaction = gstore.transaction();

transaction.run().then(() => {
    BlogPost.get(123, null, null, transaction)
            .then((entity) => {
                // Reminder: entity is an instance of the BlogPost model with all its properties & methods

                transaction.commit().then(() => { ... });
            });
});
```

**options** properties

* _preserveOrder_ \(default: false\)
* _dataloader_: a [Dataloader](https://github.com/facebook/dataloader) instance
* _cache_ \(default: true\)
* _ttl_ \(default: the cache `ttl.keys` configuration\)

&gt; **preserveOrder** option property is useful when you pass an array of IDs to retrieve and you want to preserve the order of those ids in the response.

**Note**: setting this property to _true_ does add some processing, especially for large sets. Only use it if you absolutely need to maintain the original order passed.

&gt; **dataloader** instance must be created on _each_ request. [Read the documentation](/dataloader.md) for more information on how to create the instance.

&gt; **cache** If you activated the cache on the gstore-node instance, you can override here the "global" cache configuration. If the global is set to _true_ (default) you can pass _false_ here in the options to bypass the cache. And if the global cache is set to false then you can pass true here to cache specific key(s).

&gt; **ttl** Override the ttl value of the cache. If you have multiple cache stores, you can pass an object. See in the example below.

```js
const gstore = require('gstore-node')();

// Important! This should be done on **each** request (read the Dataloader documentation)
const dataloader = gstore.createDataLoader();

BlogPost.get([1,2,3], null, null, null, { preserveOrder: true, dataloader })
        .then(function(entities) {
            // Order is preserved
            console.log(entities[0].entityKey.id); // 1
            console.log(entities[1].entityKey.id); // 2
            console.log(entities[2].entityKey.id); // 3
        });

// cache ttl options example
BlogPost.get(123, null, null, null, { ttl: 300 })
        .then(() => ... );

// cache ttl multi stores
BlogPost.get(123, null, null, null, { ttl: { memory: 60, redis: 900 } })
        .then(() => ... );

```
