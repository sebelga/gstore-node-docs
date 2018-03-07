# Model Methods

## clearCache\(\)

Clear all the queries from the cache linked to the Model Entity Kind. You can also pass one or several keys to delete from the cache.  
You normally don't have to call this method as gstore-node does it for you **automatically** each time you add/edit or delete an entity.

When updating the entities inside a transaction, gstore does not clear the cache as it does not know if the transaction is going to succeed. This is when you have to call this method manually.

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
        // Read the cache documentation for more information on this.
    });
```



