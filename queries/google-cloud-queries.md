# @google-cloud Query

gstore is built **on top of @google-cloud/datastore** so you can execute [any query from the Google library](https://cloud.google.com/nodejs/docs/reference/datastore/2.0.x/Query).

1. Create a query object `const query = MyModel.query(namespace /*optional*/, transaction /*optional*/)`
2. Chain the operators to build the query. `query.filter(...).order(...).start(...)`
3. Call query.run\(\) to execute the query. `query.run([options]).then( ... )`

## Create the query

```javascript
const query = MyModel.query(
    /* {string}. -- optional. A namespace to execute the query */
    <namespace>,
    /* {object} -- optional. A transaction to execute the query from */
    <transaction>
);
```

## Chain query operators

Refer to [@google-cloud/datastore](https://cloud.google.com/nodejs/docs/reference/datastore/2.0.x/Query) for the list of operators available.

```javascript
query.filter(...).order(...).groupBy(...).start(...);
```

## Run the query

To execute the query call `query.run(options)`

```javascript
query.run({
    /* {boolean}. -- optional. Default: false
       If set to true will return all the properties of the entity,
       regardless of the *read* parameter defined in the Schema */
    readAll: true|false,
    /* {string} -- optional. "JSON"|"ENTITY". Default: "JSON"
       Format of the response, either plain object or entity instances  */
    format: 'JSON',
    /* {boolean}. -- optional. Default: false
       Adds a "__key" property to the entity data with the complete Key from the Datastore. */
    showKey: true|false,
    /* {boolean}. -- optional. Default: the "global" cache configuration
       "true" = read from the cache and prime the cache with the query response */
    cache: true|false,
    /* {number|object} -- optional. Default: the cache.ttl.queries value
       Custom TTL value for the cache. For multi-store it can be an object of ttl values  */
    ttl: <number> | <Object>,
    /* Specify either "strong" or "eventual". If not specified, default values are chosen
       by Datastore for the operation.*/
    consistency: 'strong' | 'eventual'
);
```

**@Returns**: the response is an object with 2 properties:

* entities
* nextPageCursor // only present if there are More Results to fetch

Example:

```javascript
const User = require('./user.model');

// 1. Build the query
const query = User.query()
            .filter('firstname', '=', 'John')
            .filter('age', '>=', 4)
            .order('lastname', { descending: true })
            .limit(10);

// 2. Execute the query.

// with Promise
query.run().then((response) => {
    const entities = response.entities;
    const nextPageCursor = response.nextPageCursor; // not present if no more results
});

// or with a callback
query.run(function(err, response) {
    if (err) {
        // deal with err
    }

    const entities = response.entities;
    const nextPageCursor = response.nextPageCursor;
});

// You can then use the "nextPageCursor" when calling the same query and pass it as start value
const query = User.query()
                  .filter('name', '=', 'John')
                  .filter('age', '>=', 4)
                  .order('lastname', { descending: true })
                  .start(nextPageCursor);

// Query on namespace
const namespace = 'com.dev.server';
const query = User.query(namespace)
                .filter('name', '=', 'John');

// Query in a transaction
const transaction = gstore.transaction();
transaction.run().then(() => {
    // Create the query inside the transaction
    const query = User.query(null, transaction)
                      .filter('name', '=', 'John');

    query.run().then(() => {
        // other operations inside the transaction
        ...

        transaction.commit().then( ... )
    });    
});

// run  with options
query.run({ readAll: true, format: 'ENTITY' })
     .then( ... )

// cache ttl     
query.run({ ttl: 3600 }).then( ... )

// cache ttl multi-store
query.run({ ttl: { memory: 600, redis: 3600 }).then( ... )
```

