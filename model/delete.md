# Model Methods

## delete()

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

**@returns** an object with the following properties
    - success -- a boolean set to true if an entity has been deleted or false if not.
    - apiResponse -- the api response from google-cloud-node
    - key -- the entityKey thas has been deleted

---

Examples:

```js
const BlogPost = require('./blog-post.model');

BlogPost.delete(123, function onBlogPostDelete(err, response) {
    if (err) {
        // deal with err
    }

    if (!response.success) {
        console.log('No entity deleted. There is not BlogPost Entity with the id provided');
    }
});

// With Promise
BlogPost.delete(123).then((response) => {
    if (!response[0].success) {
        ...
    }
}).catch((err) => {
    // deal with err
});

// Array of ids
BlogPost.delete([123, 456, 789], function onBlogPostDelete(err, response) {...}

// Ancestors and a namespace
BlogPost.delete(123, ['Parent', 123], 'dev.namespace.com', function onBlogPostDelete(err, response) {...}

// Passing a key (can also be an <Array> of keys)
BlogPost.delete(null, null, null, null, key, function onBlogPostDelete(err, response) {...}

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
    return transaction.commit().then(() { ... })
}).catch((err) => {
    // handle errors 
});

// example 2 (with "pre" middleware to execute first)
transaction.run().then(() => { 
   return BlogPost.delete(123, null, null, transaction)
   	    .then(() => {
                return transaction.commit().then(() { ... });
    	    });
}).catch((err) => {
    // handle errors 
});





```