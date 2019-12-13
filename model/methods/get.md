# GET

Get one entity from the Datastore. Retrieving an entity **by key** is the fastest way to read from the Datastore.  
This method accepts the following arguments:

```javascript
MyModel.get(
    /*
     {{string | { id: string|number } | { name: string } | { key: EntityKey }}}
     */
    <id>,
    /* {object} -- optional. Additional configuration */
    <options>
)
```

**@Returns** -- a gstore entity **instance**.

Example:

```javascript
const BlogPost = require('./blog-post.model');

// id can be an integer or a string
BlogPost.get({ id: 123 }).then((entity) => {
    console.log(entity.plain());
});

// Passing an ancestor path with a Kind and a name
const ancestors = ['Parent', 'parentName'];
BlogPost.get('myEntityId', { ancestors }).then((entity) => { ... });
```

The resulting entity has a **plain\(\)** method that returns an object with the entity **data** + its **id**.  
See [the documentation of plain\(\) here](../../entity/methods/plain.md).

```javascript
BlogPost.get({ id: 123 }).then(entity) {
    console.log(entity.plain());
});
```

If you need to retrieve an entity **from inside a transaction**, you can pass the transaction object as fourth parameter.

```javascript
const transaction = gstore.transaction();

transaction.run().then(() => {
  BlogPost.get({ id: 123 }, { transaction })
    .then((blogPost) => {
      // blogPost is an instance of the BlogPost model
      // with all its properties & methods
      transaction.commit().then(() => { ... });
  });
});
```

### **options** properties

* `ancestors` 
* `transaction` 
* `namespace`
* `preserveOrder` \(default: `false`\)
* `dataloader`: a [Dataloader](https://github.com/facebook/dataloader) instance
* `cache` \(default: `true`\)
* `ttl` \(default: the global cache `ttl.keys` configuration\)

&gt; **ancestors**: Ancestors path for the entity \(eg: `['ParentEntity', 'parentName' ]`\)

&gt; **transaction**: Optional transaction to execute the get request.

&gt; **namespace**: Optional namespace for the entity.

&gt; **dataloader** Optional Dataloader to use to fetch the entity. [Read the documentation](../../cache-dataloader/dataloader.md) for more information on how to create a `dataloader` the instance.

&gt; **cache** If you activated the cache on the gstore-node instance, you can override here the **global** cache configuration. If the cache **for keys fetching** has been activated \(default: `true`\), you can pass `false` here to bypass the cache. If the cache for keys fetching has been disabled, then you can pass `true` here to cache specific key\(s\).

&gt; **ttl** Overrides the global keys TTL of the cache. If you have multiple cache stores, you can pass an _Object_ with a different value for each store. See in the example below.

```javascript
const { instances } = require('gstore-node');

const gstore = instances.get('default');

// Important! This should be done on **each** request (read the Dataloader documentation)
const dataloader = gstore.createDataLoader();

BlogPost.get([1,2,3], null, null, null, { preserveOrder: true, dataloader })
        .then((entities) => {
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

