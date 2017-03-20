#Queries

## @google-cloud Query

gstore is built **on top of @google-cloud/datastore** so you can execute [any query from the Google library](https://googlecloudplatform.github.io/google-cloud-node/#/docs/datastore/master/datastore/query).  

1. Create a query object `const query = MyModel.query(namespace /*optional*/, transaction /*optional*/)`
`
2. and chain the operators to build the query. `query.filter(...).order(...).start(...)`
3. call query.run() to execute the query. `query.run([options]).then( ... )`


### Create the query

```js
const query = MyModel.query(
    /* {string}. -- optional. A namespace to execute the query */
    <namespace>,
    /* {object} -- optional. A transaction to execute the query from */
    <transaction>
);
```

### Chain query operators

Refer to [@google-cloud/datastore](https://googlecloudplatform.github.io/google-cloud-node/#/docs/datastore/master/datastore/query) for list of operators

```js
query.filter(...).order(...).groupBy(...).start(...);
```  

### Run the query
To execute the query call

```js
query.run(
    /* {boolean}. -- optional. Default: false
       If set to true will return all the properties of the entity, regardless of the *read* parameter defined in the Schema */
    <readAll>,
    /* {string} -- optional. "JSON"|"ENTITY". Default: "JSON"
       Format of the response, either plain object or entity instances  */
    <format>
);
```

Example:
```js
constUser = gstore.model('User'/ with User schema previously defin

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