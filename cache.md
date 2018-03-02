# Cache

![](https://github.com/sebelga/gstore-cache/raw/master/logo/logo.gif)gstore-node integrates fully with the [gstore-cache](https://github.com/sebelga/gstore-cache) manager. Let's say that gstore-cache has been created for gstore-node but it has been made available in a separate npm package so that it can be used without gstore-node...

The advantage of using gstore-cache with gstore-node is that all the management of the cache \(set/get/delete\) is done for you. Just initialize gstore with the cache turned on and call it a day :\)

gstore-cache uses underneath the powerful [node-cache-manager](https://github.com/BryanDonovan/node-cache-manager). This means that you can have multiple cache stores with different TTL settings for each one and that, out of the box, you get a [LRU memory cache](https://www.npmjs.com/package/lru-cache) instance.

## Activate the cache

You activate the cache by passing a configuration object during the gstore initialization. You can also just pass **true** and the default cache config of gstore-cache will be used.

#### Default configuration

```js
// server.js (Application Bootstrap)

require('gstore-node')({ cache: true }); 

// ------------------------------
// Default config:
// ------------------------------
const default = {
    stores: [
        {
            store: 'memory',
            max: 100,
        },
    ],
    ttl: {
        keys: 60 * 10, // 10 minutes
        queries: 5, // 5 seconds
        // the "stores" setting below is only used when there are multiple stores
        stores: {
            memory: {
                keys: 60 * 5, // 5 minutes
                queries: 5,
            },
            redis: {
                keys: 60 * 60 * 24, // 1 day
                queries: 0, // infinite
            },
        },
    },
    cachePrefix: {
        keys: 'gck:', // Gstore Cache Key
        queries: 'gcq:', // Gstore Cache Query
    },
};
```

#### Custom configuration

Refer to the gstore-cache documentation for a detailed explanation of the config properties.

```js
// server.js (Application Bootstrap)

const cacheConfig = {
    stores: [{ store: 'memory', max: 200 }],
    ttl: { keys: 1200 },
    queries: -1, // don't cache queries
};

const gstore = require('gstore-node')({ cache: cacheConfig }); 

// ...

```

#### Access the cache instance

You can access any time the underlying gstore-cache instance and call its methods. You might want to cache other data than the keys or queries returned by gstore-node. Just call the cache set/mset/get/mset/del methods directly to manage your cache.

```js
// Anywhere in your Appication
const gstore = require('gstore-node')();

// The cache is a "gstore-cache" instance
const { cache } = gstore;

// You can then call any method from gstore-cache

cache.get('somekey').then(...);
cache.set('someKey', { name: 'john' }).then(...);
```

You can of course desactivate \(or activate it\) on a per request basis, or change the TTL \(time to live\) value.

