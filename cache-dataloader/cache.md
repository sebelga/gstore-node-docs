# Cache

![](https://github.com/sebelga/nsql-cache/raw/master/logo/logo.gif)

gstore-node integrates the **[nsql-cache](https://github.com/sebelga/gstore-cache)** manager. This means that the cache management \(add, remove, invalidate) is done for you. Just initialize gstore-node with the cache turned on and call it a day.

nsql-cache uses underneath the powerful [node-cache-manager](https://github.com/BryanDonovan/node-cache-manager). This means that you can have multiple cache stores with different TTL settings in each one. It also means that you get a [LRU memory cache](https://www.npmjs.com/package/lru-cache) instance out of the box that will give a boost to your application right away.

**Important:** Since v2.7.0 of node-cache-manager it is possible to set/get and delete multiple keys at once from the cache stores. Although this feature is available in the manager, you still need to provide a store that _supports_ it. At the time of this writing, only the _memory_ store and the [node-cache-manager-redis-store](https://github.com/dabroek/node-cache-manager-redis-store) support it. If you provide [another store engine](https://github.com/BryanDonovan/node-cache-manager#store-engines) that does not support _mget_ or _mset_ you will still be able to use the cache but you won't be able to fetch **multiple** keys (batch) from the Datastore and cache them.

## Activate the cache

You activate the cache by passing a configuration object during the gstore initialization. You can also just pass **true** and the default cache configuration will be used.

#### Default configuration

```js
// server.js (Application Bootstrap)
const GstoreNode = require('gstore-node');

// Default cache configuration
const gstore = GstoreNode({ cache: true }); 

// Or by passing cache store(s) and configuration
const gstore = GstoreNode({
    cache: {
        stores: [ ... ],
        config: { ... }
    },
});

```

This is the default configuration from nsql-cache.

```js
// ------------------------------
// Default cache stores
// ------------------------------

const defaultCacheStores = [
    {
        store: 'memory', // LRU cache
        max: 100, // maximum number of item in the cache
    },
];

// ------------------------------
// Default configuration
// ------------------------------

const defaultConfig = {
    ttl: {
        keys: 60 * 10, // 10 minutes
        queries: 5, // 5 seconds

        // the setting below is only when there are multiple cache stores
        memory: {
            keys: 60 * 5, // 5 minutes
            queries: 5,
        },
        redis: {
            keys: 60 * 60 * 24, // 1 day
            queries: 0, // infinite
        },
    },
    cachePrefix: {
        keys: 'gck:', // Gstore Cache Key
        queries: 'gcq:', // Gstore Cache Query
    },
    hashCacheKeys: true,

    // turn the cache "ON" globally.
    // If you set it to false, you will have to activate it
    // on each request
    global: true,
};
```

Refer to the [nsql-cache](https://github.com/sebelga/nsql-cache#api) for  a detailed explanation of the configuration properties.


#### Multi stores example

To change the configuration you only need to provide what needs to be overriden.

```js
...
const redisStore = require('cache-manager-redis-store');

const cacheStores = [{
    store: 'memory',
    max: 200,
}, {
    store: redisStore,
    host: 'localhost', // default value
    port: 6379, // default value
    // ... any other configuration for Redis client
}];

const cacheConfig = {
    ttl: {
        memory: {
            keys: 10, // 10 seconds
            queries: 5, // 5 seconds
        },
        redis: {
            keys: 60 * 60,
            queries: 60 * 60
        },
    }
};

const gstore = require('gstore-node')({
    cache: {
        stores: cacheStores,
        config: cacheConfig
    }
}); 
```

## Access the cache instance

You can access at any time the underlying gstore-cache **instance** and call its API. If you need to cache custom data \(other the than _keys_ or _queries_ managed by gstore-node\), just call the set/mset/get/mset/del methods directly on the cache instance.  
For more information on the API refer to the [nsql-cache documentation](https://github.com/sebelga/nsql-cache#api).

```js
// Anywhere in your Appication
const gstore = require('gstore-node')();

const { cache } = gstore;

// You can then call any method from gstore-cache
cache.get('somekey').then(...);
cache.set('someKey', { name: 'john' }).then(...);
```

## Advanced Cache for Queries

nsql-cache has an advanced cache for queries **when you provide a **_**Redis**_** store** \(either as single store or in a multi-store\). nsql-cache detects the _Kind_ of the entities on which the query is run to not only cache the _response_ of the Query but also keep a _reference_ to the Query in a Redis **_Set_**. It creates one Set by _Entity Kind_.

This means that you can have an infinite TTL \(0\) for the queries on the Redis store. Each time an entity is added/updated/deleted gstore-node will automatically remove all the queries references from the Entity Kind Set and invalidate the cache of those queries.

### Useful methods

All the cache management of nsql-cache is done for you by gstore-node. There is though one useful method that you might need:

#### `gstore.cache.queries.kset(key, data, entityKinds)`

In case you have a complex aggregation of data that comes from multiple queries, `cache.queries.kset()` lets you save its data in the cache and **link** Entiy Kinds to it. Later, if _any_ Entity Kind passed here is added/updated or deleted, gstore will invalidate the cache for this data.

`kset()` \(for **k**ind **s**et\) takes 3 arguments:

* _key_ : a custom cache key you want to give to this data
* _data_: the data to cache
* _entityKinds_: one or multiple entityKinds related to the data

Let see it with an example:

```js
const gstore = require('gstore-node')();
const { cache } = gstore;

const Posts = require('./posts.model');
const Users = require('./users.model');

/**
 * Handler to fetch all the data for our Home Page
 */
const fetchHomeData = () => {
    // Check the cache first...
    cache.get('website:home').then(data => {
        if (data) {
            // in the cache... great!
            return data;
        }

        // Cache not found, query the data
        const queryPosts = Posts.query()
            .filter('category', 'tech')
            .limit(10)
            .order('publishedOn', { descending: true });

        const queryTopStories = Posts.query()
            .order('score', { descending: true })
            .limit(3);

        const queryProducts = Products.query().filter('featured', true);

        return Promise.all([queryPosts.run(), queryTopStories.run(), queryProducts.run()])
            .then(result => {
                // Build our data object
                const homeData = {
                    posts: result[0],
                    topStories: result[1],
                    products: result[2],
                };

                // We save the result of the 3 queries to the cache ("website:home" key)
                // and link the data to the "Posts" & "Products" Entity Kinds.
                // We can now safely keep the cache infinitely, gstore will take care
                // of clearing the cache when a "Posts" or "Products" is added/edited or deleted.
                return cache.queries.kset('website:home', homeData, ['Posts', 'Products']);
        });
    });
};
```

## Transactions

For the most part you don't have to worry about clearing the cache as gstore-node automatically does it for you each time you add/edit or delete an entity.  
It cannot do it for you though when you are updating entities inside a transaction as it does not know if the transaction succeeded or not. So you will have to clear the cache manually after a transaction succeeds.

Let see it with an example.

```js
const gstore = require('gstore-node')();
const transaction = gstore.transaction();

const User = require('./user.model');
const Post = require('../posts/post.model');

transaction.run()
    .then(() => {
        return User.get(123, null, null, transaction)
            .then((user) => {
                const post = new Post({ title: 'My new Blog Post', authorId: user.id });
                post.save(transaction);

                // We update the total of posts for this user
                user.totalPosts += 1;
                user.save(transaction);

                transaction.commit()
                    .then(() => {
                        // Transaction successful... we need to clear the cache now

                        // 1. Delete the cache for "User" Entity Kind
                        // ---> both the key passed **and** the queries linked to "User" will be deleted
                        const promise1 = User.clearCache(User.key(123));

                        // 2. Delete the cache for "Post". Here no key is passed, we just want
                        // to make sure that all query data cached linked to "Post" is deleted
                        const promise2 = Post.clearCache();

                        return Promise.all([promise1, promise2]);
                    });
            });

    })
```



