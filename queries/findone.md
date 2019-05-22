# findOne\(\)

Quickly find an entity by passing key/value pairs. You can optionally pass an ancestors array or a namespace.

**@Returns** an gstore entity **instance** of the Model.

This method accepts the following arguments:

```javascript
MyModel.findOne(
    /* {object}. -- Key/Value pairs to look for */
    <propsValues>,
    /* {Array} -- optional. ex: ['ParentEntity', 1234 ] */
    <ancestors>,
    /* {string} -- optional. A specific namespace */
    <namespace>,
    /* {object}. -- optional. Options for the query */
    <options>
)
```

## options

The options argument accepts the following properties:

* **cache** \(default: the "global" cache configuration\) "true" = read from the cache and prime the cache with the query response.
* **ttl** \(default: the global `cache.ttl.queries` value\) Custom TTL value for the cache. For multi-store it can be an _Object_ of TTL values.
* **readAll** \(default: false\). Return all the properties, even the ones marked as `read: false` on the Schema.

Example:

```javascript
const User = require('./user.model');

User.findOne({ email: 'john@snow.com' })
    .then((entity) => {
        console.log(entity.plain()); // entityData + id
        console.log(entity.firstname)); // 'John'
});

// Disable cache and read all properties
User.findOne({ email: 'john@snow.com' }, null, null, { cache: false, readAll: true })
    .then((entity) => {
        console.log(entity.plain()); // entityData + id
        console.log(entity.firstname)); // 'John'
});

// Custom TTL for cache
// ttl set to "-1" means to not cache for this store
User.findOne({ email: 'john@snow.com' }, null, null, { ttl: { memory: -1, redis: 300 } })
    .then((entity) => {
        console.log(entity.plain()); // entityData + id
        console.log(entity.firstname)); // 'John'
});
```

