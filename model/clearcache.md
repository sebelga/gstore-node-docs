# Model Methods

## clearCache()

Clear all the queries from the cache linked to the Model Entity Kind. You can also pass on or several keys to clear from the cache at the same time.
You normally don't have to call this method as gstore-node does it for you automatically each time you add/edit or delete an entity.

When inside a transaction, gstore does not clear the cache as it does not know if the transaction is going to succeed. 

the keys from the cache 

```js
MyModel.clearCache(
    /* {key|Array<key>}. -- optional. One or several keys to remove from the cache */
    <key>
).then( ... );
```

Example

```js
const User = require('./user.model');

const key1 = User.key(123);
const key2 = User.key(456, ['Ancestor', 'default']);

User.clearCache([key1, key2])
    .then(() => {
        // Not only the 2 keys have been removed from the cache
        // but also any Query linked to the entity kind "User".
        // Read the cache documentation for more info on this.
    });
```


