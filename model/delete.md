# Model Methods

## delete()

You can delete an entity by calling `Model.delete(...args)`.  

This method accepts the following arguments:

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

**@returns** a boolean set to true if an entity has been deleted or false if not.

```js
var BlogPost = gstore.model('BlogPost');

BlogPost.delete(123, function(err, response) {
    if (err) {
        // deal with err
    }
    if (!response.success) {
        console.log('No entity deleted. The id provided didn\'t return any entity');
    }
    
    // The response has a *key* property with the entity keys that have been deleted (single or Array)
});

// With an array of ids
BlogPost.delete([123, 456, 789], function(err, success, apiResponse) {...}

// With ancestors and a namespace
BlogPost.delete(123, ['Parent', 123], 'dev.namespace.com', function(err, success, apiResponse) {...}

// With a key (can also be an <Array> of keys)
BlogPost.delete(null, null, null, null, key, function(err, success, apiResponse) {...}


// Transaction
// -----------
/* The same method can be executed inside a transaction
 * Important!: if you have "pre" middelware set fot delete, then you must *resolve*
 * the Promise before commiting the transaction
*/

var transaction = gstore.transaction();

transaction.run(function(err) {
    if (err) {
        // handle error
        return;
    }
		
	// example 1 (in sync when there are no "pre" middleware)
	BlogPost.delete(123, null, null, transaction); 
	
	transaction.commit(function(err) {
        if (err) {
            // handle error
        }
	});
	
   // example 2 (with "pre" middleware to execute first) 
   BlogPost.delete(123, null, null, transaction)
   				.then(() => {
    				 transaction.commit(function(err) {
				        if (err) {
				            // handle error
				        }
				    });
    			});
});

```