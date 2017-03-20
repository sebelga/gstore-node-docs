#Queries

## @google-cloud Query

gstore is built **on top of @google-cloud/datastore** so you can execute [any query from the Google library](https://googlecloudplatform.github.io/google-cloud-node/#/docs/datastore/master/datastore/query).  

1. First you create a query object with:  
`const query = MyModel.query(namespace /*optional*/, transaction /*optional*/)`  
then chain all the operators.  
2. Then you call query.run() to execute the query.  
Query.run() has an **optional** parameters to pass 2 settings: "readAll" and "format".

```
/**
 * readAll:
 * If you set it to true, all the properties will be returned regardless of the "read"
 * setting defined on the Schema
 *
 * format: 
 * Response format, either plain object (default) or entity instances
*/

{
    readAll: true | false
    format : gstore.Queries.formats.JSON | gstore.Queries.formats.ENTITY
}
```

```js
var User = gstore.model('User'); // with User schema previously defined

// 1. Initialize query
var query = User.query()
            .filter('name', '=', 'John')
            .filter('age', '>=', 4)
            .order('lastname', {
                descending: true
            });

// 2. Execute query.
// The callback response contains both the entities and the cursor if there are more results

query.run(function(err, response) {
    if (err) {
        // deal with err
    }

    // response contains both the entities and a nextPageCursor for pagination
    var entities       = response.entities;
    var nextPageCursor = response.nextPageCursor; // not present if no more results
});

// You can then use the nextPageCursor when calling the same query and set it as a start value
var query = User.query()
            .filter('name', '=', 'John')
            .filter('age', '>=', 4)
            .order('lastname', {
                descending: true
            })
            .start(nextPageCursor);

// Example with an options parameter
query.run({ readAll:true, format: gstore.Queries.formats.ENTITY })
	 .then( ... );

```

**namespace**  
Model.query() takes an optional "namespace" parameter if needed.

```js
var query = User.query('com.domain-dev')
                .filter('name', '=', 'John');
...
```

**transaction**  
Model.query() takes an optional "transaction" parameter if needed.

```js

var transaction = gstore.transaction();
transaction.run().then(() => {

	// Create the query inside the transaction
	var query = User.query(null, transaction)
                .filter('name', '=', 'John');
   
   	query.run().then(() => {
  		...
  		
  		// transaction.commit()...
   	});	
});

...

```

If no callback is passed, a **Promise** is returned

```js
var query = User.query()
            .filter('name', '=', 'John');

query.run().then((result) => {
    const response = result[0];

    // response contains both the entities and a nextPageCursor for pagination
    var entities       = response.entities;
    var nextPageCursor = response.nextPageCursor; // not present if no more results
});
```