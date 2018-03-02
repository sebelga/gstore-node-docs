# Cache

![](https://github.com/sebelga/gstore-cache/raw/master/logo/logo.gif)gstore-node integrates fully with the [gstore-cache manager](https://github.com/sebelga/gstore-cache). Caching the entities \(both Keys and Queries\) and clearing the cache is automatically done for you. Just initialize gstore with the cache turned on and call it a day :\)

gstore-cache uses the powerful [node-cache-manager](https://github.com/BryanDonovan/node-cache-manager) underneath. This means that you can have several cache stores with different TTL settings for each store.

## Activate the cache

You activate the cache passing a configuration object to the gstore initialization. You can also just pass **true** and the default cache configuration of gstore-cache will be used.

#### Default settings

```js
// server.js (Application Bootstrap)

const gstore = require('gstore-node')({ cache: true }); 

//
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

