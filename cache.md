# Cache

![](https://github.com/sebelga/gstore-cache/raw/master/logo/logo.gif)gstore-node integrates fully with the [gstore-cache manager](https://github.com/sebelga/gstore-cache). Caching the entities \(both Keys and Queries\) and clearing the cache is automatically done for you. Just initialize gstore with cache "on" and call it a day :\)

## Activate the cache

You activate the cache passing a configuration object to the gstore initialization. You can also just pass **true** and it will use the default configuration of gstore-cache.

#### Default settings

```js
// server.js

const gstore = require('gstore-node')({ cache: true }); 

// 
```



#### Access the cache instance

```js
// Anywhere in your Appication
const gstore = require('gstore-node')();

// The cache is a gstore-cache instance
const { cache } = gstore;

// You can then call any method from gstore-cache

cache.get('somekey').then(...);
cache.set('someKey', { name: 'john' }).then(...);  
```

You can of course desactivate \(or activate it\) on a per request basis, or change the TTL \(time to live\) value.

