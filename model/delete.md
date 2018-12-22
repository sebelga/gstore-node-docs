# Model Methods

This method allows you to delete an entity in the Datastore. It accepts the following arguments:

```js
MyModel.delete(
    /* {int|string}. -- Can also be an Array of ids to delete */
    <id>,
    /* {Array} -- optional. ex: ['ParentEntity', 1234 ] */
    <ancestors>,
    /* {string} -- optional. A specific namespace */
    <namespace>,
    /* {Transaction} -- optional. The transaction currently in progress */
    <transaction>,
    /*
        {object|Array} -- optional. An entityKey. Can also be an *Array* of keys.
        If you already know the key (after a get() for ex.) you can pass it here.
    */
    <key>,
    /* {function} -- optional. The callback, if not passed a Promise is returned */
    <callback>
)
```

**@returns** -- an object with the following properties
    - success -- a boolean set to true if one or serveral entity(ies) has/have been deleted or false if not.
    - key -- the entity key(s) that have been deleted
    - apiResponse -- the api response from @google-cloud-node
---

Examples:

```js
const BlogPost = require('./blog-post.model');

BlogPost.delete(123).then((response) => {
    if (!response.success) {
        console.log('No entity deleted. There is not BlogPost Entity with the id provided');
    }
}).catch((err) => {
    // deal with err
});

// array of ids
BlogPost.delete([123, 456, 789]).then((response) => { ... });

// ancestors and a namespace
BlogPost.delete(123, ['Parent', 123], 'dev.namespace.com').then((response) => { ... });

// passing an entity Key (can also be an *Array* of keys)
BlogPost.delete(null, null, null, null, key).then((response) => { ... });

// with a callback
BlogPost.delete(123, function onBlogPostDelete(err, response) {
    if (err) {
        // deal with err
    }
    
    ...
});


// Transaction
// -----------
/* The same method can be executed inside a transaction
 * Important: if you have "pre" middelware defined on "delete" in your schema,
 * then you must first *resolve* the Promise before commiting the transaction
*/

const transaction = gstore.transaction();

// example 1 -- with no "pre" delete middleware
transaction.run().then(() => {
    BlogPost.delete(123, null, null, transaction); 
    transaction.commit().then(() { ... })
}).catch((err) => {
    // handle errors 
});

// example 2 (with "pre" middleware to execute first)
transaction.run().then(() => ( 
   BlogPost.delete(123, null, null, transaction)
           .then(transaction.commit)
   	    .then(() => {
                ...
    	    });
)).catch((err) => {
    // handle errors 
});

```
